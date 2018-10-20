# Render Prop: why prefer using this.props.children over this.props.render

When ever I use `render prop` , I prefer passing the state via `this.props.children` over `this.props.render` . The first reason is for the sake the consistency since the `Context API` also using `children`. The second and the more important is it enhances your code transparency. For example:

```jsx
<YourComponent 
    firstProps={...} 
    render={(props) => (
        // Very very very 
        // very long JSX
    )} 
    myVeryImportantProps={...}/>
```

In the case, your `JSX` will be very long and if you write another prop below the `render`, then it could affect the visibility of your code. 



