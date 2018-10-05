---
layout: post
title: "How We Grew Out of Bootstrap"
date: 2013-02-14 09:06
comments: true
categories: ['cheezburger','tech','front end dev']
---
When we started our redesign of the Cheezburger network we had the daunting task of building a front end framework that met the needs of not only our developers but our vast network of sites. After many meetings, grueling discussions and attempts at DIY we finally choose [twitter] [Bootstrap](http://getbootstrap.com). It was the 'it' framework and had just reached its 2.0 milestone, so why not?<!-- more -->

Going into this we had no idea how to take bootstrap and make it Cheezburger. We initially tried to "extend" Bootstrap by overriding styles in our own chzboot library. This proved to be less than ideal since we were duplicating code for everything we wanted to extend. Eventually we merged our changes with Bootstrap core and forfeited any upgrade path to newer versions.

Our own pseudo fork of Bootstrap has worked well throughout. However as our design has changed and matured we have began to migrate away from the Bootstrap core components and build our own. It was nice to have Bootstrap to fallback on when we needed a quick UI treatment for something, but now we have our own style guide and documentation around what components to use and how to use them. If something isn't in the style guide then we need to build and add it to the bucket of things that can be reused later.

It has become difficult to develop our own components along with maintaining existing Bootstrap based components. Finding parity between what Bootstrap offers and the needs of our sites is the one thing that has effectively driven us away from continuing to use Bootstrap as our core foundation. We have talked about completely removing anything Bootstrap from our code and rewriting components, but that is a task that isn't logical at this time. Eventually as we continue to mature our code Bootstrap will play less of a role.

Ultimately we grew out of bootstrap not because it's a bad framework but because we grew up, we started needing things that were not in its core. We changed so much it didn't make sense to continue to maintain a connection to the core library. We have maintained many of the naming and structural conventions that bootstrap employs but we don't rely on its library of UI elements.

One thing I didn't mention, when we made the decision to use Bootstrap over creating our own I pushed for it out of fear of "re-inventing the wheel"†. Throughout this experience I have come to believe you should not fear re-inventing the wheel, you should fear using a round wheel when you need a square one.

<small>† I don't want to speak for [@b_sted](http://twitter.com/b_sted), [@marthakelly](http://twitter.com/marthakelly), or [@trademark](http://twitter.com/trademark) but this was my biggest fear going into the redesign.</small>
