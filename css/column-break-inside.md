# Column break-inside

Columns do a great job of flowing and balancing content. Unfortunately, not all elements flow gracefully. Sometimes elements get stuck between columns.

![Use break-inside to avoid this default behavior](../.gitbook/assets/image.png)

```css
-webkit-column-break-inside: avoid; /* Chrome, Safari, Opera */
          page-break-inside: avoid; /* Firefox */
               break-inside: avoid; /* IE 10+ */
```



