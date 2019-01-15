# Web Accessibility

[WAI-ARIA](http://www.w3.org/TR/wai-aria/) is a spec defining support for accessible web apps. It defines bunch of markup extensions \(mostly as attributes on HTML5 elements\), which can be used by the web app developer to provide additional information about the semantics of the various elements to assistive technologies like screen readers. Of course, for ARIA to work, the HTTP user agent that interprets the markup needs to support ARIA, but the spec is created in such a way, as to allow down-level user agents to ignore the ARIA-specific markup safely without affecting the web app's functionality.

Here's an example from the ARIA spec:

```markup
<ul role="menubar">

  <!-- Rule 2A: "File" label via aria-labelledby -->
  <li role="menuitem" aria-haspopup="true" aria-labelledby="fileLabel"><span id="fileLabel">File</span>
    <ul role="menu">

      <!-- Rule 2C: "New" label via Namefrom:contents -->
      <li role="menuitem" aria-haspopup="false">New</li>
      <li role="menuitem" aria-haspopup="false">Open…</li>
      ...
    </ul>
  </li>
  ...
</ul>
```

Note the `role` attribute on the outer `<ul>` element. This attribute does not affect in any way how the markup is rendered on the screen by the browser; however, browsers that support ARIA will add OS-specific accessibility information to the rendered UI element, so that the screen reader can interpret it as a menu and read it aloud with enough context for the end-user to understand \(for example, an explicit "menu" audio hint\) and is able to interact with it \(for example, voice navigation\).  


#### **Further read:**

{% embed url="https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA\_Techniques" %}

{% embed url="https://www.w3.org/WAI/GL/wiki/Using\_ARIA\_menus" %}

{% embed url="https://rawgit.com/w3c/aria-practices/master/aria-practices-DeletedSectionsArchive.html\#ariaBackground" %}

