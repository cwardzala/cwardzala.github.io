---
layout: post
title: Solving Mysteries in Magento
comments: true
categories: ['tech', 'Magento', 'eCommerce', 'PHP']
---
Not long ago our Magento eCommerce website started logging a strange and cryptic exception.<!-- more -->

```
PHP Fatal error:  Uncaught exception 'Exception' with message 'Serialization of 'Mage_Core_Model_Con
fig_Element' is not allowed' in [no active file]:0\nStack trace:\n#0 {main}\n  thrown in [no active file] on line 0
```

With no context about what this means I turned to Google. Little of the results helped trace down what the issue was. Since it never appeared to affect our users I pushed it to the side to solve another day.

Around the time the exception began, I noticed alert messages were never getting cleared. A message would just stay around until the user logged out and cleared their session.

After a few months, I found time to dig into the messages issue. I started poking around at our extension of Magento's built in messages class. Once I ruled that out as the source of the issue, I moved on to more aggressive measures to find the issue. I turned on every debug option I could find. With DEBUG ALL THE THINGS mode turned on I saw our old cryptic message popping up again. This led me to dig into that once again. Google didn't provide much in the way of solutions, but it did point me in the direction of our session storage. I began poking around and looking for anywhere we put something into session. Once I ruled out `string` and `int` types as an issue, I looked at other data types. EUREKA! I found a function trying to store an instance of the `Findaway_MultiCart_Model_Quote` Class. Commenting out the code and reloading proved that was the source.

Moral of the story, test and look for exceptions related to new code. It's amazing this was so easy to fix given it was so difficult to track down. This could have been quicker had PHP exposed a more useful message, or had Magento raised an exception.
