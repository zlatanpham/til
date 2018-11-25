# CSS trigger

When you change a CSS properties value, the browser needs to react to your change. Some values change the layout of the page. For instance, a change in `width` requires the browser to update layout, _then_ "paint" any pixels that have changed, _then_ "composite" them together. That's a lot of work. Some CSS properties can be changed more inexpensively. For instance, a change in `background-image`doesn't require any layout changes, but does require paint and composite. [https://csstriggers.com/](https://csstriggers.com/).

![](https://developers.google.com/web/fundamentals/performance/rendering/images/intro/frame-full.jpg)

**Further reading**: [https://developers.google.com/web/fundamentals/performance/rendering/](https://developers.google.com/web/fundamentals/performance/rendering/)

