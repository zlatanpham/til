# The performance benefits of rel=noopener

If you have links to another origin, you should use `rel="noopener"`, especially if they open in a new tab/window.

```markup
<a href="http://example.com" target="_blank" rel="noopener">
   Example site
</a>
```

Without this, the new page can access your window object via `window.opener`. Thankfully the origin security model of the web prevents it reading your page, but no-thankfully some legacy APIs mean it _can_ navigate your page to a different URL using `window.opener.location = newURL`.

Web superhero Mathias Bynens [wrote about this in detail](https://mathiasbynens.github.io/rel-noopener/), but I just discovered there's a performance benefit too.

## Demo

The random numbers act like a heartbeat for this page. If random numbers aren't being generated every frame, something is holding up the thread.

Now click one of these to open a page that runs some expensive JavaScript:

* [`<a target="_blank">`](https://cdn.rawgit.com/jakearchibald/787311bb8645ed3f65cc98ea74ebf269/raw/5316ed16abfa4b2bd0125f2d2aa5fec06394befb/index.html)
* [`<a target="_blank" rel="noopener">`](https://cdn.rawgit.com/jakearchibald/787311bb8645ed3f65cc98ea74ebf269/raw/5316ed16abfa4b2bd0125f2d2aa5fec06394befb/index.html)

Without `rel="noopener"`, the random numbers are disrupted by the new page's JavaScript. Not only that, all main-thread activity is disrupted - try selecting text on the page. But with `rel="noopener"` the random numbers keep generating at 60fps. Well, in Chrome & Opera anyway.

**Update:** Edge doesn't experience jank for either link because it doesn't support `window.opener` for `_blank` links.

## So why does this happen?

Most browsers are multi-process with the exception of Firefox \(and [they're working on it](https://developer.mozilla.org/en-US/Firefox/Multiprocess_Firefox)\). Each process has multiple threads, including what we often call the "main" thread. This is where parsing, style calculation, layout, painting and non-worker JavaScript runs. This means JavaScript running on one domain runs on a different thread to a window/tab running another domain.

However, due to the synchronous cross-window access the DOM gives us via `window.opener`, windows launched via `target="_blank"` end up in the same process & thread. The same is true for iframes and windows opened via `window.open`.

`rel="noopener"` prevents `window.opener`, so there's no cross-window access. Chromium browsers optimise for this and open the new page in its own process.

## Site isolation

Here in Chrome HQ we're looking at [moving cross-domain iframes and new windows into their own process](http://www.chromium.org/developers/design-documents/site-isolation) even if they don't have `rel="noopener"`. This means the limited cross-window access will become asynchronous, but the benefit is improved security and performance.

In the meantime, `rel="noopener"` gives you the performance & security benefit today!



**Origin**: [https://jakearchibald.com/2016/performance-benefits-of-rel-noopener/](https://jakearchibald.com/2016/performance-benefits-of-rel-noopener/)

