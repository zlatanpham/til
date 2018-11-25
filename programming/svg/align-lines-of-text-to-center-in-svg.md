# Align lines of text to center in SVG

If you add `text-anchor="middle"` to _each_ `tspan` you will center them \(you have to remove the space _between_ the tspans as well, otherwise the extra space will be considered as part of the first line and they won't be completely centered\).

```text
<svg>
  <text>
    <tspan x="0" text-anchor="middle"> First line </tspan>
    <tspan x="0" text-anchor="middle"> Second line </tspan>
  </text>
</svg>
```

