# Cascading SVG Fill Color

One time someone told me their biggest problem with SVG icons is that they didn't match the color of text they were by. In fact it was such a big problem for them, that despite seeing the advantages of SVG icons they were sticking with icon fonts. I didn't think of it at the time, but there is a pretty easy solution for this.

```markup
<h1>
  <svg viewBox="0 0 32 32">
    <use xlink:href="#icon-phone"></use>
  </svg>
  Call Me
</h1>
```

```css
h1 {
  color: blue;
}
h1 svg {
  fill: currentColor;
}
```

Or more simple

```css
h1 {
  color: blue;
  fill: currentColor;
}
```

