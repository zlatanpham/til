# CSS

## Get rid of extra space below svg in div element

```markup
<div style="background-color: red;">
  <svg height="100px" width="100" style="background-color: blue;"></svg>
</div>
```

^ will leave a white-space below the `svg`. To get rid of the space you need `display: block;` on your `svg`.

```markup
<svg style="display: block;"></svg>
```

This is because inline-block elements \(like `<svg>` and `<img>`\) sit on the text baseline. The extra space you are seeing is the space left to accommodate character descenders \(the tail on 'y', 'g' etc\).

You can also use `vertical-align:top` if you need to keep it `inline` or `inline-block`

