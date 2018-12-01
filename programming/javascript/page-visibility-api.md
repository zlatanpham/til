---
description: >-
  The Page Visibility API is especially useful for saving resources and
  improving performance by letting a page avoid performing unnecessary tasks
  when the document isn't visible.
---

# Page Visibility API

With tabbed browsing, there is a reasonable chance that any given webpage is in the background and thus not visible to the user. The Page Visibility API provides events you can watch for to know when a document becomes visible or hidden, as well as features to look at the current visibility state of the page.

When the user minimizes the window or switches to another tab, the API sends a [`visibilitychange`](https://developer.mozilla.org/en-US/docs/Web/Events/visibilitychange) event to let listeners know the state of the page has changed. You can detect the event and perform some actions or behave differently. For example, if your web app is playing a video, it can pause the video when the user puts the tab into the background, and resume playback when the user returns to the tab. The user doesn't lose their place in the video, the video's soundtrack doesn't interfere with audio in the new foreground tab, and the user doesn't miss any of the video in the meantime.

Visibility states of an [`<iframe>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe) are the same as the parent document. Hiding an `<iframe>`using CSS properties \(such as [`display: none;`](https://developer.mozilla.org/en-US/docs/Web/CSS/display)\) doesn't trigger visibility events or change the state of the document contained within the frame.

### Use cases

Let's consider a few use cases for the Page Visibility API.

* A site has an image carousel that shouldn't advance to the next slide unless the user is viewing the page
* An application showing a dashboard of information doesn't want to poll the server for updates when the page isn't visible
* A page wants to detect when it is being prerendered so it can keep accurate count of page views
* A site wants to switch off sounds when a device is in standby mode \(user pushes power button to turn screen off\)

Developers have historically used imperfect proxies to detect this. For example, watching for [`blur`](https://developer.mozilla.org/en-US/docs/Web/Events/blur) and [`focus`](https://developer.mozilla.org/en-US/docs/Web/Events/focus) events on the window helps you know when your page is not the active page, but it does not tell you that your page is actually hidden to the user. The Page Visibility API addresses this.

> Note: While [onblur](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onblur) and [onfocus](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onfocus) will tell you if the user switches windows, it doesn't necessarily mean it's hidden. Pages only become hidden when the user switches tabs or minimizes the browser window containing the tab.

### Policies in place to aid background page performance

Separately from the Page Visibility API, user agents typically have a number of policies in place to mitigate the performance impact of background or hidden tabs. These may include:

* Most browsers stop sending [`requestAnimationFrame()`](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame) callbacks to background tabs or hidden [`<iframe>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe)s in order to improve performance and battery life.
* Timers such as [`setTimeout()`](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout) are throttled in background/inactive tabs to help improve performance. See [Reasons for delays longer than specified](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout#Reasons_for_delays_longer_than_specified) for more details.
* Budget-based background timeout throttling is now available in modern browsers \(Firefox 58+, Chrome 57+\), placing an additional limit on background timer CPU usage. This operates in a similar way across modern browsers, with the details being as follows:
  * In Firefox, windows in background tabs each have their own time budget in milliseconds — a max and a min value of +50 ms and -150 ms, respectively. Chrome is very similar except that the budget is specified in seconds.
  * Windows are subjected to throttling after 30 seconds, with the same throttling delay rules as specified for window timers \(again, see [Reasons for delays longer than specified](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout#Reasons_for_delays_longer_than_specified)\). In Chrome, this value is 10 seconds.
  * Timer tasks are only permitted when the budget is non-negative.
  * Once a timer's code has finished running, the duration of time it took to execute is subtracted from its window's timeout budget.
  * The budget regenerates at a rate of 10 ms per second, in both Firefox and Chrome.

Some processes are exempt from this throttling behavior. In these cases, you can use the Page Visibility API to reduce the tabs' performance impact while they're hidden.

* Tabs which are playing audio are considered foreground and aren’t throttled.
* Tabs running code that's using real-time network connections \([WebSockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) and [WebRTC](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API)\) go unthrottled in order to avoid closing these connections timing out and getting unexpectedly closed.
* [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) processes are also left unthrottled in order to avoid timeouts.

**In detail**: [https://developer.mozilla.org/en-US/docs/Web/API/Page\_Visibility\_API](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API)

