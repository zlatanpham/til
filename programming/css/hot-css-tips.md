# Hot-css-tips

  
**Image rendering**  


```css
img.QRcode {  image-rendering: pixelated;}
```

This property is useful to render QR codes and image thumbnails to increase their quality.

**Check empty nodes**

```css
my-component:empty {  display: none;}
```

Hide your element when have no content inside. `Returns` and `white spaces` are considered as content.

**Shape your text**

```css
p {  shape-outside: polygon(0 0, 0 200px, 300px 600px);}
```

Change the way how content will wrap around your floated elements.

**Plain SVG as background**

```css
my-component {  background-image: url('data:image/svg+xml;utf8,<svg>...</svg>');}
```

Use `<svg>` as css background without convert it to base64.

**Disable all user interactions**

```css
[data-untouchable] {  pointer-events: none;}
```

Disable all user interactions, even js events, with just one property.

**Clean and self-contained component**

```css
my-component {  all: initial;  contain: content;}
```

Create a style-cleaned and self-contained component with two properties. With `contain: content` will be created a new stacking-context and position fixed coordinates will reference to this property.

**Overflow snapping**

```css
my-component {
  scroll-snap-type: mandatory;
  scroll-snap-align: center;
}
```

Control scroll snapping for elements with overflow.

**System Font Stack**

```css
my-component {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI",
                Helvetica, Arial, Ubuntu, sans-serif,
                "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
}
```

Use the system font to provide a consistent typography experience.

**Check if input have a value**

```css
.Note {
  opacity: 0;
  transition: opacity 200ms ease-out;
}

input:not(:placeholder-shown) + .Note {
  opacity: 1;
}
```

This pseudo class allows you to check if the input have a value. [Live demo.](https://jsfiddle.net/equinusocio/9hdm3fLc/embedded/result/)

**Repeat Gradients**

```css
.RepeatLinear {
  background:
    repeating-linear-gradient(
      45deg,
      lime,
      lime 10px,
      pink 10px,
      pink 20px
    );
}

.RepeatRadial {
  background:
    repeating-conic-gradient(
      circle at 0 0,
      tomato,
      limegreen 50px
    );
}
```

You can repeat gradients instead of mess with mixins.

**Vector icons as mask**

```css
button {  
    background: linear-gradient(to right, #d2ff52 0%,#30A85A 100%);  
    height: 40px;  width: 100px;  
    mask: url(https://cdn.onlinewebfonts.com/svg/img_529012.svg);  
    mask-repeat: no-repeat;  mask-position: center;  mask-size: 24px;
}

```

Use svg icons as mask and keep color manipilation. [Live demo.](https://jsfiddle.net/equinusocio/2jekbdas/embedded/result/)

**Float based on document direction**

```css
img {  float: inline-start; /* ...or inline-end */}
```

Float an element based on the text direction \(`rtl` or `ltr`\).

**Target default form element**

```css
input:default {  opacity: 0.2;}
```

Target the default selected input in a gruop. [Live demo.](https://jsfiddle.net/equinusocio/kn231bx9/embedded/result/)

**Import your css when you need it**

```css
@import url('portrait.css') screen and (orientation: portrait);
```

You can conditionally import your css like you can do inline with the `<link>`. Please consider that vanilla css imports are a bad thing in terms of performance because they start an potentially [infinite calls chain.](https://image.ibb.co/mZOcBd/Screen_Shot_2018_07_02_at_11_37_16.png).

**Improve touchable elements based on pointer precision**

```css
@media (any-pointer: fine) { /* I'm default */ }@media (any-pointer: coarse) { /* I'm a bit larger. I have a less precise pointer method */ }
```

Improve your touchable elements if the main pointer input is `precise` \(mouse\) or `less precise` \(touch\).

**Prevent over-scroll**

```css
.ScollingContent {  overscroll-behavior: contain;}
```

Prevent over-scroll when the scroll reach the end of the element

