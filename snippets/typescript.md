# TypeScript

### Fix Object.keys return string\[\]

```typescript
const items = {a: ...};
const keys = Object.keys(items) as Array<keyof typeof items>;
```

### Keys of array \(typescript 3.4\)

```typescript
const myArray = <const> ['foo', 'bar'];
type MyArray = typeof myArray[number]; // => type MyArray = 'foo' | 'bar'
```

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

### Object with specific properties

```typescript
export type StyleString = 'absolute' | 'relative';

type Styles = { [key in StyleString]: CSSObject };

// This will enforce 
const styles : Styles = {
    absolute: {...},
    relative: {...}
}
```

In this way, you can reuse the `StyleString` as well as do not have to write boilerplate interface:

```typescript
interface Styles{
    absolute: CSSObject;
    relative: CSSObject;
    .....
}
```

### Use generic for a component having a prop accepting other component

Generic is useful to maximize the flexibility of a component. Following demonstrates a `ParentComponent` enforces the `ChildComponent` to have a prop `isActive` but still allowing the `ChildComponent` having its own props through generic: 

```typescript
interface MyComponentProps<T> {
  ChildComponent: React.ComponentType<ChildComponentProps<T>>;
  childProps: T;
}

type ChildComponentProps<T> = T & {
  isActive: boolean;
};

interface MyComponentState {
  isActive: boolean;
}

class MyComponent<T> extends Component<MyComponentProps<T>, MyComponentState> {
  state = {
    isActive: false,
  };
  render() {
    const { ChildComponent, childProps } = this.props;
    return (
      <div>
        <ChildComponent isActive={this.state.isActive} {...childProps} />
      </div>
    );
  }
}
```

Quick usage:

```typescript
interface CustomChildProps {
  count: number;
}

interface ChildProps extends CustomChildProps {
  isActive: boolean;
}

const CustomChild = ({ count, isActive }: ChildProps) => (
  <div>{isActive && count}</div>
);

class CustomComponent extends MyComponent<CustomChildProps> {}

class App extends Component {
  render() {
    return (
      <div className="App">
        <CustomComponent
          childProps={{ count: 2 }}
          ChildComponent={CustomChild}
        />
      </div>
    );
  }
}
```

