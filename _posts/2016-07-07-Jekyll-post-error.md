---
layout: post
title:  "Jekyll post not showing"
date:   2016-07-07 13:00:00 +0200
description: "Made a post and it wasn't appearing on the blog."
tags: blog, github, jekyll, website, site, error
---
I am new to Jekyll, but thought it's a simple way to publish my fixes to the great web.

## My problem
I haven't read much of Jekyll's documentation, instead I thought I would go through them as I become familiar with the workings of Jekyll.
I made a post about [Wix cab in msi]({% post_url 2016-07-06-Wix-cab-in-msi.md %}), however, I noticed that it was not appearing on this blog.

## My fix
Again, [Stackoverflow](https://stackoverflow.com/questions/30625044/jekyll-post-not-generated) came to the rescue. I was aware that setting the **date** field ahead would not post until that date, but I was not aware of anything else.

My problem was that I had a ```:``` in my title, and replacing it with ```&#58``` instead worked.
