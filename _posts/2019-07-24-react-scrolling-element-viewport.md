---
layout: post
title: Animate a DOM element when it scrolls into view
date: 2019-07-24
categories:
---

[Demo page](https://liamst19.github.io/react-scroll-demo/)

One thing I have been noticing while browsing websites is that a lot of them have animated effects that are triggered when they scroll into view. I was curious how they did that, so [I tried to recreate the effect](https://github.com/liamst19/react-scroll-demo).

After looking around it seemed that the simplest way is to use add an event handler to the `onScroll` event, which compares the top and bottom positions of the element with that of the `document`'s. In more recent browsers, the element dimensions were provided with the `Element.getBoundingClientRect()` function:

``` javascript
// Checks whether the element is in viewport
function isInViewport(element){
    var rect = element.getBoundingClientRect();
    
    return rect.top >= 0 
        && rect.bottom <= (window.innerHeight 
            || document.documentElement.clientHeight);
    // If we're also checking horizontally, append
    // (rect.left >= 0 
        && (window.innerWidth 
            || document.documentElement.clientWidth))
}
```

And inside the event listener, use the above test and then add (e.g., `element.classList.add('animate-elem')`) and remove (e.g., `element.classList.remove('animate-elem')`) a class name which triggers show/hide css animation.

References:

 - [How to test if an element is in the viewport with vanilla JavaScript ](https://gomakethings.com/how-to-test-if-an-element-is-in-the-viewport-with-vanilla-javascript/) - Go Make Things blog
