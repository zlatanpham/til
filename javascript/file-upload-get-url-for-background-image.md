---
description: >-
  Get preview image path after selecting an image from your computer via file
  input.
---

# Preview for file input

Given the case you want to get image url to be used by CSS `background-image:url()` in order to display the preview for a file upload. Use [`URL.createObjectURL()`](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL) static method creates a [`DOMString`](https://developer.mozilla.org/en-US/docs/Web/API/DOMString) containing a URL representing a given [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File) object or [`Blob`](https://developer.mozilla.org/en-US/docs/Web/API/Blob) object.

```javascript
var imagePath = URL.createObjectURL(file[0]);
```



