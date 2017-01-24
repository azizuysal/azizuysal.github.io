---
layout: post
comments: true
title: Migrated blog to Jekyll/Github Pages
---

I've finally migrated this blog to Github Pages using Jekyll. This was something I wanted to do for a long time but didn't have any time. It took me a couple of hours and went mostly smoothly.

I followed [Barry Clark's blog post](https://www.smashingmagazine.com/2014/08/build-blog-jekyll-github-pages/) to fork and use his [jekyll-now](https://github.com/barryclark/jekyll-now) repo as a starting point. That was the easy part.

Exporting my old wordpress.com blog was slightly more complex. I first used wordpress.com's export option to export everything in my blog in an xml file. I then used a tool called [exitwp](https://github.com/thomasf/exitwp) to convert all of that data into Jekyll format, which I then copied under `_posts` folder in my new Jekyll blog.

And the trickiest part was to import comments into Disqus. Even though comments are included in the wordpress.com export file, they are not imported into Jekyll, and Jekyll has no built-in support for comments as it's only a static site generator. So enter Disqus. I first set up an account in Disqus and then created a site and obtained a Disqus shortname. I then proceeded to create a site, selected Jekyll template, and added the YAML Front Matter to my posts as instructed. Then I imported my WordPress export xml file using the Import options on Disqus web site. However, that was not enough, as the URL slugs had changed when posts were imported to Jekyll. So I had to go to Disqus Migration Tools, select Start URL Mapper option, download existing URLs from their link, edit the CSV to add corrected URLs, and then re-upload the fixed CSV file. And, voila! All my old comments from the old wordpress.com site was there.

There is still some more work to do I think. I noticed all images still point to the wordpress.com site, I'll have to download the images and change the image references. Also code highlighting is not working properly and I'll have to update code snippets with proper MarkDown notation.

Overall, it was a nice learning experience and hopefully time well spent as it should pay back by reducing the time and friction to upload new posts in the future.
