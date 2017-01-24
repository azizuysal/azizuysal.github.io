---
author: azizuysal
comments: true
date: 2007-12-24 14:59:23+00:00
layout: post
link: https://azizuysal.wordpress.com/2007/12/24/changing-wordpress-permalink-structure/
slug: changing-wordpress-permalink-structure
title: Changing WordPress Permalink Structure
wordpress_id: 14
categories:
- Software
tags:
- wordpress
---

![permalink.png](http://azizuysal.files.wordpress.com/2010/12/permalink.png)

After adding a few pages to my blog, I realized that the permalink structure of my blog (i.e. http://www.azizuysal.com/?p=7) is a bit ugly and uninformative. Particularly, reading statistics like Google Analytics becomes more difficult as it is hard to understand which page the reports are referring to from the ugly URLs. So I decided to make a change and I checked a few of my favorite blogs for inspiration. I found out that most of them use a "/year/month/day/postname/" structure for their permalinks.

Following the simple instructions I found [here](http://www.searchenginejournal.com/seo-friendly-url-structure/4556/), I was able to change my permalinks to a much more friendly, and informative, "/year/month/postname/" structure. I also installed the [Permalink Redirect plugin](http://scott.yang.id.au/code/permalink-redirect/) for wordPress so that any external references to old style links will be redirected to my new permalinks.
