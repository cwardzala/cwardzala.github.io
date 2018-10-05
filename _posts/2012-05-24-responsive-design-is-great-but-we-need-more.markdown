---
layout: post
title: "Responsive Design is Great but We Need More."
comments: true
categories: [code, responsive]
---

As we enter a day and age where everything needs to be “responsive” we need to have a better way of identifying and supporting devices. <!-- more -->

Responsive design is a great technique and can yield some awesome results but it is just a stop gap to a greater issue. We can make our css responsive and we can make our javascript responsive but what about actually supporting devices and device features?

One of the main issues right now with web development and supporting multiple devices is device identification. we all know that the browser’s user agent is flawed, so we don’t use it... right? Wrong, more often than not it’s the default way of identifying what browser and platform you are using. This is terrible.

We need a new way of providing browser information. What way is that, who knows.

Why can’t the browser provide an object with this information? would it be so hard?

    // an example of such object could be
    browser.info = {
        name : "Chrome | Internet Explorer | firefox | Opera | etc...",
        mobile : true | false,
        version : 19.0.1084.46
    }

This isnt 1999 we don’t need to fake what browser we are using, browsers are powerful enough and support most of the same standards that what the user agent has become isnt needed.

That only solves one problem, replacing useragent for identification. How do we actually support devices like phones and tablets?

There are some specs being written to allow supporting things like responsive images but they don't really support a platform only visual and physical requirements like width/height and pixel density. To truly support devices we need to know more, we need to have access to system information that is currently not available to the browser.

If there can be a spec for HTML5 vibration why can’t there be a spec for network speed? This to me is a problem of not focusing on important features to add and focusing on a niche.

In my opinion we need a system of knowing what network speed we are currently on before we need the ability to vibrate a device.

As device usage grows these types of questions will need to be answered and issues will need to be fixed. Maybe this borders on “native” apps but to me this feels like something that needs to be addressed to keep web development progressing.
