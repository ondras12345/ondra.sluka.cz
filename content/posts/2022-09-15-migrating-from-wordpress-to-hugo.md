---
draft: false
date: 2022-09-15T12:50:18+02:00
title: "Migrating this website from WordPress to Hugo"
description: >-
  A list of reasons why I think Hugo is better than WordPress for my use-case.
featured_image: ""
tags:
  - WWW
categories:
  - software
series:
math: true
---
I have recently migrated my WordPress-based website
https://ondra.sluka.cz/wp/ to [Hugo](https://gohugo.io/), a framework for
building static websites.

All content that is still relevant and potentially interesting has been moved
over, and I have added some new content, too.

## Why use Hugo instead of WordPress?
I see many advantages:

1. **Static website**. Static websites are faster to load, easier to deploy
   and require virtually no maintenance.

1. **Content can be written in markdown**. I've always hated WordPress'
   editor. For example, it was very hard to get code block formatting right.
   It kept stripping out leading spaces from my Python programs, thus removing
   indentation and breaking them. Markdown allows you to use your favorite
   text editor (Vim) to write the content. You can also directly include HTML
   tags and even use so-called
   [shortcodes](https://gohugo.io/content-management/shortcodes/)
   to keep your website
   [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).

1. **(Almost) no security vulnerabilities**. The only code running on the
   server is the web server itself (e.g. Apache httpd).
   No WordPress vulnerabilities and no vulnerable plug-ins. All Hugo templates
   are rendered before deploying to the server.

1. If you choose the right theme, **many things work out of the box**. Code
   blocks with syntax highlighting for all imaginable programming languages.
   ```python
   for i in range (10):
       print(i)
   ```
   LaTeX maths.
   $$X_C = \frac{1}{2 \pi f C}$$
   Automatically generated RSS feeds, etc.

1. **Easy version control with `git`**.

1. **Does not miss basic features when rendered without JavaScript**.
   The main menu with the most important links on the website just didn't show
   up without JavaScript on WordPress. That's unacceptable for me. I don't
   want to force the visitors of my website to use JavaScript.


The Hugo version is going live now at https://ondra.sluka.cz. If everything
goes well, I'll get rid of the WordPress instance at
https://ondra.sluka.cz/wp/ in the near future.
