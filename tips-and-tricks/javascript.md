# JavaScript

### Create a range

```javascript
Array.from({length: 9}, (a, i) => i)
// [0, 1, 2, 3, 4, 5, 6, 7, 8]
```

### Quickly convert Nodelist to Array

```javascript
const myArray = [...nodeList]

// useful when
[...document.querySelectorAll('li')].forEach(node => { // doUseFulStuff })
```

