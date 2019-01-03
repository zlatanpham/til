# React

### Trick to measure Render calls

Place `RenderCount` inside a component, the counter will be added by 1 while its parent `re-render`. This is a tricky way to detect render waste.

```jsx
class RenderCount extends React.Component {
  renders = 0;
  render() {
    return <span>{++this.renders}</span>;
  }
}
```

### ​Media queries for css-in-js

```jsx
const mq = {
  sm: '@media (min-width: 576px)',
  md: '@media (min-width: 768px)',
  lg: '@media (min-width: 992px)',
  xl: '@media (min-width: 1200px)',
};

export const Container = styled('div')({
  padding: '30px',
  [mq['sm']]: {
    padding: '20px',
  },
});
```

