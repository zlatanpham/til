# TypeScript

### Ignore library missing type definition

```typescript
// @ts-ignore: component missing type defs
import library from 'library'
```

### Type for React HTMLElement

```jsx
export const Button = ({
  text,
  active = false,
  ...rest
}: ButtonProps & React.HTMLAttributes<HTMLButtonElement>) => (
  <button active={active} {...rest}>    
    {text}
  </button>
);
```

### Extend and overwrite 

```typescript
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;

interface BaseInterface{
  A: string,
  B: number
}

export interface ExtendedInterface
  extends Omit<BaseInteface, "A"> {
  A: number
  C: number
}
```

### Binding events

Binding events to a DOM node

```jsx
// If
componentDidMount(){
    document.addEventListener('mouseup', this.handleMouseUp);
}
// then
handleMouseUp = (e: MouseEvent) => {};
```

However, if the method was bound to a `React` component

```jsx
// If
render(){
    return <div onMouseUp={this.handleMouseUp} />
}
// then
handleMouseUp = (e: React.MouseEvent<HTMLDivElement>) => {};
```

### Render prop children

```jsx
interface StateAndHelperType{
    // Type definition
}

interface ComponentProps{
    children: (args: StateAndHelpersType) => JSX.Element
}
```

### Partial

Sometimes we partially want to get some properties from an interface but not required to know those properties, particularly, in case of React `setState`.  Then `Partial` will match the case:

```typescript
type Partial<T> = { [P in keyof T]?: T[P] };

interface MyComponentState{
    propertyOne: number,
    propertyTwo: string,
    propertyThree: boolean
}

type StateToSetType =
  | ((prevState: MyComponentState) => Partial<MyComponentState>)
  | Partial<MyComponentState>;

class MyComponent extends React.Component<any, MyComponentState>{
  internalSetState = (stateToSet: StateToSetType, cb?: () => void) => {
    return this.setState((state: FilterMenuItemState) => {
      return typeof stateToSet === 'function' ? stateToSet(state) : stateToSet;              
    }, cb);
  };
}
```

