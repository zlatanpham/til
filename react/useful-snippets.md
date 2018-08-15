# Useful snippets

### Use render-prop to pass function

```jsx
class Wrapper extends Component{
    handleClick = () => {};    
    render = () => this.props.children(this.handleClick)
}
```

Usage:

```jsx
<Wrapper>
{
    handleClick => <button onClick={handleClick}>Click me</button>
}
</Wrapper>
```



