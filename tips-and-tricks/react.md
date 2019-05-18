# React

### Reduce the complexity of useReducer if the state is simple

Instead of the tradition params of `state` and `action`, treat `action` as the `newState` and do the merging.

```jsx
import {useReduce} from 'react'

function reducer = (oldState, newState) => ({...oldState, ...newState})

function Component() {
    const [{count}, dispatch] = useReducer(reducer, {count: 0 });
    
    render <button onClick={() => dispatch({count: count + 1});}>{count}</button>
}
```

### Create constant with useRef

useRef can serve as a constant inside a function component to prevent reinitiate a variable if the function re-called. 

```jsx
import {useRef, useEffect} from 'react'

function Component(){
    const firstRenderRef = useRef();
    const intervalRef = useRef();
    
    useEffect(() => {
        if(!firstRenderRef.current){
            doOnlyForFirstRenderCall();
            firstRenderRef.current = false;
        }
        intervalRef.current = window.setInterval(() => {...}, 100)
        () => {
            window.clearInterval(intervalRef.current)
        }
    },[])        
}
```

### Run console.log in JSX

```jsx
return (
    console.log(‘foo foo’) || <Component argument={console.log(foo) || bar} />
)
```

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

