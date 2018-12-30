# TypeScript

## Ignore library missing type definition

```typescript
// @ts-ignore: component missing type defs
import library from 'library'
```

## Type for React HTMLElement

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

## Extend and overwrite 

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

