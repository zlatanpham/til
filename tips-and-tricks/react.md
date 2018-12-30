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

​

