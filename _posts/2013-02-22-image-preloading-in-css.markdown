---
layout: post
title: "Image Preloading in CSS"
date: 2013-02-22 09:59
comments: true
categories: ['tech', 'front end dev']
---

Something that I hadn't thought about in a while was image preloading in css.<!-- more -->
The other day I sent [Matt](http://busticated.org) a code review because the way he was
preloading an image was causing some odd behavior. We talked about it and could not think of a good solution, or better preload option so I removed his preload css (below).

    html {
        background: url(/path/to/image.jpg) no-repeat -1000px;
    }

In most cases I think this method would work but in our case it was causing pages with short content to have an unstyled block of white on the bottom of the page.
I wasn't sure how to tell him to fix this, we really didnt want to go the old JS route but could not find a way to do this in CSS.

Then for no reason I was poking around the source of <http://foundation.kr> and found this little gem that made total sense to me.

    body:before {
        content: url() url() ...;
        display:none;
    }

My first thought was "Oh my gosh this is amazing!". Then I started wondering if the image would download because `display:none`. I updated our code to use this method of
preloading and IT WORKED! I grabbed Matt and showed him and he asked the same question I did about the display property. Because the image url is the content
of the pseudo element it gets downloaded regardless of the display propery. Unlike background images which don't get downloaded until the image display is something other than none.

And the best part about this method is you can have as many `url()` in the content and they will all get downloaded!
