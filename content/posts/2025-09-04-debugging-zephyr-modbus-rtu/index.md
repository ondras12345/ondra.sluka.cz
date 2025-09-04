---
draft: false
date: 2025-09-04T18:37:20+02:00
title: "Debugging Modbus RTU in Zephyr RTOS"
description: >-
  This article documents how I spent 4 hours debugging issues with the Modbus
  subsystem in the Zephyr RTOS, and how I fixed them.

  Should be worth a read if your commands are failing with "Calculated CRC
  does not match received CRC" warnings in the log.
featured_image: "pulseview-detail.png"
tags:
  - Zephyr
  - microcontroller
categories:
  - electronics
series:
---
{{< figure src="pulseview-detail.png" >}}

I am currently working on a project that requires controlling a motor VFD via
RS-485 [Modbus RTU](https://en.wikipedia.org/wiki/Modbus).
I am using a custom PCB built around the
[W55RP20-EVB-PICO](https://docs.wiznet.io/Product/ioNIC/W55RP20/w55rp20-evb-pico)
evaluation board with the RP2040 MCU. Among other things, my PCB adds the
THVD1400DR RS485 transceiver IC to communicate with the VFD at 38400 baud.

## The problem
I was trying to read the VFD's parameters with the 0x03 "Read Multiple Holding
Registers" modbus command. I could read up to 6 registers just fine, but if I
tried more, I kept getting these CRC errors:
```
uart:~$ vfd pardump 1-10
parameter       value
read failed (-5): addr=1, count=10
[00:21:44.172,000] <dbg> modbus_serial: rtu_tx_adu: uart_buf
                                        01 03 00 01 00 0a 94 0d                          |........       
[00:21:44.172,000] <dbg> modbus_serial: rtu_tx_adu: Start frame transmission
[00:21:44.183,000] <dbg> modbus_serial: cb_handler_rx: rx, n=16
[00:21:44.186,000] <dbg> modbus_serial: modbus_rtu_rx_adu: uart_buf
                                        01 03 14 00 00 00 00 00  00 00 00 17 3f 00 1c 01 |........ ....?.
[00:21:44.186,000] <wrn> modbus_serial: Calculated CRC does not match received CRC
```

## Debugging
Just a quick note: this debugging was done with **Zephyr 4.2.0**. If you are
running an older version, go update immediately before wasting hours debugging
other Modbus issues like I did. It turns out the older versions did not flush
the Rx buffer properly, and after an error, all following read commands would
receive the *previous* command's response.

First of all, the debug log presented above has been obtained after applying
the following patch to add more debug messages:
```patch
diff --git a/subsys/modbus/modbus_serial.c b/subsys/modbus/modbus_serial.c
index a4a4d29e870..7b750d15323 100644
--- a/subsys/modbus/modbus_serial.c
+++ b/subsys/modbus/modbus_serial.c
@@ -261,6 +261,8 @@ static int modbus_rtu_rx_adu(struct modbus_context *ctx)
 	uint16_t crc_idx;
 	uint8_t *data_ptr;
 
+	LOG_HEXDUMP_DBG(cfg->uart_buf, cfg->uart_buf_ctr, "uart_buf");
+
 	/* Is the message long enough? */
 	if ((cfg->uart_buf_ctr < MODBUS_RTU_MIN_MSG_SIZE) ||
 	    (cfg->uart_buf_ctr > CONFIG_MODBUS_BUFFER_SIZE)) {
@@ -372,6 +374,7 @@ static void cb_handler_rx(struct modbus_context *ctx)
 				   (CONFIG_MODBUS_BUFFER_SIZE -
 				    cfg->uart_buf_ctr));
 
+		LOG_DBG("rx, n=%i", n);
 		cfg->uart_buf_ptr += n;
 		cfg->uart_buf_ctr += n;
 	}
```

After analyzing it, we can see that the `rtu_timer` in Zephyr's
`modbus_serial.c` expires before the full response is received from the VFD.

By using my [Bus Pirate 5 as a logic
analyzer](https://docs.buspirate.com/docs/logic-analyzer/pulseview-sump/),
I determined that the VFD was sending its response correctly with no delay
between the individual bytes:

{{< figure src="pulseview.png"
    caption="Modbus communication between the VFD and Zephyr captured on a logic analyzer"
>}}

```
6686-7729 Modbus: Client->server: Slave ID: 1
8118-9161 Modbus: Client->server: Function 3: Read Holding Registers
9550-12025 Modbus: Client->server: Start at address 0x1 / 30002
12415-14890 Modbus: Client->server: Read 10 units of data
15279-17754 Modbus: Client->server: CRC correct
34922-35965 Modbus: Server->client: Slave ID: 1
36354-37397 Modbus: Server->client: Function 3: Read Holding Registers
37786-38829 Modbus: Server->client: Byte count: 20
39217-41692 Modbus: Server->client: 0x0000 / 0
42080-44555 Modbus: Server->client: 0x0000 / 0
44943-47418 Modbus: Server->client: 0x0000 / 0
47806-50281 Modbus: Server->client: 0x0000 / 0
50669-53144 Modbus: Server->client: 0x173F / 5951
53532-56007 Modbus: Server->client: 0x001C / 28
56395-58870 Modbus: Server->client: 0x012D / 301
59258-61733 Modbus: Server->client: 0x001F / 31
62121-64596 Modbus: Server->client: 0x0000 / 0
64984-67459 Modbus: Server->client: 0x001F / 31
67847-70322 Modbus: Server->client: CRC correct
```


### UART interrupts
Notice this line in the log:
```
<dbg> modbus_serial: cb_handler_rx: rx, n=16
```
How was the UART interrupt handler able to read 16 bytes at once?
It turns out the PL011 UART in the RP2040 only fires an interrupt when its
Rx FIFO buffer fills up, or after it hasn't received anything for a while.
This means that after the first 16 bytes of the response arrive,
the `rtu_timer` starts ticking, waiting for the next byte within the next
millisecond or so. That byte does arrive in time, but it gets stuck
waiting in the FIFO. By the time the FIFO fills up with the next 16 bytes
and the Rx interrupt fires, the timer had already expired.

## The fix
This fix is simple: I just added the
[`fifo-disable`](https://docs.zephyrproject.org/latest/build/dts/api/bindings/serial/arm%2Cpl011.html)
property to the UART in the devicetree:
```dts
&uart0 {
    current-speed = <38400>;
    status = "okay";
    pinctrl-0 = <&uart0_default>;
    pinctrl-names = "default";
    fifo-disable;

    modbusvfd: modbus0 {
        compatible = "zephyr,modbus-serial";
        status = "okay";
        de-gpios = <&gpio0 2 GPIO_ACTIVE_HIGH>;
    };
};
```

Now we get one interrupt for every received byte, and the timer does not
expire prematurely.

Before:
```
uart:~$ vfd pardump 100-115
parameter       value
read failed (-5): addr=100, count=16
[00:25:11.924,000] <dbg> modbus_serial: rtu_tx_adu: uart_buf
                                        01 03 00 64 00 10 05 d9                          |...d....       
[00:25:11.924,000] <dbg> modbus_serial: rtu_tx_adu: Start frame transmission
[00:25:11.934,000] <dbg> modbus_serial: cb_handler_rx: rx, n=16
[00:25:11.937,000] <dbg> modbus_serial: modbus_rtu_rx_adu: uart_buf
                                        01 03 20 00 c8 00 00 00  01 00 01 00 00 01 f4 00 |.. ..... ......
[00:25:11.937,000] <wrn> modbus_serial: Calculated CRC does not match received CRC
```

After:
```
uart:~$ vfd pardump 100-115
parameter       value
P100    200
P101    0
P102    1
P103    1
P104    0
P105    500
P106    250
P107    20
P108    30
P109    3800
P110    500
P111    190
P112    25
P113    95
P114    12
P115    10
[00:04:07.396,000] <dbg> modbus_serial: rtu_tx_adu: uart_buf
                                        01 03 00 64 00 10 05 d9                          |...d....        
[00:04:07.397,000] <dbg> modbus_serial: rtu_tx_adu: Start frame transmission
[00:04:07.401,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.402,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.402,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.402,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.403,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.403,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.403,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.403,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.404,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.404,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.404,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.405,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.405,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.405,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.405,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.406,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.406,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.406,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.407,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.407,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.407,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.407,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.408,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.408,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.408,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.409,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.409,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.409,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.409,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.410,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.410,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.410,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.411,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.411,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.411,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.411,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.412,000] <dbg> modbus_serial: cb_handler_rx: rx, n=1
[00:04:07.413,000] <dbg> modbus_serial: modbus_rtu_rx_adu: uart_buf
                                        01 03 20 00 c8 00 00 00  01 00 01 00 00 01 f4 00 |.. ..... .......
                                        fa 00 14 00 1e 0e d8 01  f4 00 be 00 19 00 5f 00 |........ .......
                                        0c 00 0a 10 56                                   |....V           
uart:~$
```
