---
layout: post
title: "Using SignalR with RequireJS"
date: 2012-11-09 10:16
comments: true
categories: [tech, javascript]
---

If you don’t know about SignalR and RequireJS

* <https://github.com/SignalR/SignalR>
* <http://requirejs.org>

When I was building my app with SignalR and RequireJS there were two steps I needed before stuff would work.
<!-- more -->
## 1. Setup dependencies

I needed to wrap all my SignalR javascripts so they will work properly with RequireJS.

    require.config({
        paths: {
            jquery: 'jquery-1.8.2.min'
        },
        shim : {
            "jquery.signalR-0.5.3": ['jquery'],
            "/signalr2/hubs": ['jquery', 'jquery.signalR-0.5.3'],
        }
    });

Using the great shim feature of RequireJS 2.* I can use non AMD compatible scripts with RequireJS without manually wrapping them in define() statements. I also set the path for jquery because it is a named module and is required by the SignalR scripts.

## 2. Fix SignalR $.connection.hub.start()

I am calling SignalR's $.connection.hub.start() from within a $(document).ready(). Even before I started using RequireJS to load my scripts this worked fine. However after moving to RequireJS this stopped working. Why? basically SignalR will by default delay starting your connection until the page has completely loaded. Enter the `waitForPageLoad` option on $.connection.hub.start(). This tells SignalR if you want to wait for the page to load before starting your connection which in my case I don't. Simply set that option to `false` and it will start the connection when run instead of waiting. This fixed my issues and SignalR and RequireJS started playing nice together. Note: I am also using a callback on start() to make sure my connection has been established before using my server methods.

    $(document).ready(function () {
        $.connection.hub.start({waitForPageLoad:false},function () {
            // callback code
        });
    });

