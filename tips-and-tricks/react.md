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

### State Reducer Pattern

Although the state of a component should be encapsulated, it's sometimes useful to break the rule to maximize the flexibility of your component.

```jsx
class MyComponent extends React.Component{
    internalSetState = (stateToSet, callback) => {
        this.setState(prevState => {
            let newStateToSet = typeof stateToSet === 'function' ? stateToSet(prevState) : stateToSet;
            // Pass through a stateReducer
            newStateToSet = this.props.stateReducer(prevState, newStateToSet);            
            
            const newState = {};
            Object.keys(newStateToSet).forEach(key => {
                if(prevState[key] !== newStateToSet[key]){
                    nextState[key] = newStateToSet[key]
                }
            });            
            
            return newState;
        }, callback)
    }
    // Then use this.internalSetState instead of this.setState
}

class App extends React.Component{
    // stateReducer(prevState, newStateToSet)
    stateReducer(currentState, changes){
        const newState = // Some modification based on currentState & changes                
        return newState;
    }
    
    render(){
        <MyComponent stateReducer={this.stateReducer}/>
    }
}
```

