---
description: From Big Containers to Small Connected Components
---

# Lessons Learned Using React-Redux

When we first started using React with Redux at [Gloat](https://gloat.com/) we usually had a large connected container component that selected the relevant state and passed it down to all of the components under this container. **Over time the complexity of our app and its components grew and this led to various problems in our code.**

**First** — performance, the entire component and its children had to render on every change. In most cases the store state changes affect only a small part of the UI so rendering the whole page for that change is pretty wasteful.

**Second** — it made our code harder to refactor when we needed to change how we handle the state that was passed into the components. In other words, it got messy. Many components are aware of props that they don’t really care about and just pass these props down to their children.

**Third** — code duplication/boilerplate, every time we need to render a common piece of data in a child component, we need to connect the container to our state. We had many areas with duplicate code in different containers bringing common state data into our app.

Here’s an example of such a case:Used with [https://jdittrich.github.io/quickMockup/](https://jdittrich.github.io/quickMockup/)

![](https://cdn-images-1.medium.com/max/800/1*uwc3jRelcZ6cQ_-D_NCazg.png)

```jsx
let UserProfileContainer = ({ userName }) => (
    <Grid>
        <NavBar userName={userName} />
       {/* ...other profile UI components... */}
    </Grid>
);

const mapStateToProps = state => ({
    userName: `${state.user.firstName} ${state.user.lastName}`,
    //other meaningful props...
});

UserProfileContainer = connect(mapStateToProps)(UserProfileContainer);

const NavBar = ({ userName }) => (
    <nav>
        {/* ...navigation items */}
        <UserDropdown userName={userName} />
    </nav>
);

const UserDropdown = ({ userName }) => (
    <NavDropdown title={userName}>
        <MenuItem>Settings</MenuItem>
        <MenuItem>Logout</MenuItem>
    </NavDropdown>
);
```

As you can see `NavBar` and `UserDropdown` are aware of the userName prop although only the `UserDropdown` component really uses this data.

This makes our code harder to maintain and easily causes unwanted bugs. Lets say I want to refactor my userName prop to a different name, I’m going to need to change it in all of the related components — `UserProfileContainer, NavBar` and `UserDropdown`. That’s 3 places when only 1 component needed this data.

**Plus, what if there are other areas in the app in which we need to display the user’s name?** We will need to connect the component and parse the name from the state, which can lead to a lot of code duplication. I can reuse the parsing logic with a small helper, but we’re still going to need to connect\(\) this other component.

**And performance — now when the user name is changed in the state it will cause all 3 components to render while we only need** `UserDropdown`**component to update.**

This is a very simplified case, often there are plenty of props passed down through 3–4 components because we use single container components that are connected to the state.

**Is there a better way?**

Luckily we were not the only ones who encountered these issues with React-Redux. At [Gloat](http://www.gloat.com/) we use [_Redux_-_form_](https://redux-form.com/) to implement our form logic and UI, at some point they had a major breaking changes release due to a complete overwrite of the library. Taken from their docs:

> In `v5`, only the outer form component was connected to the Redux state, and the props for each field were passed in via the form component. The problem with this is that the _entire_ form component had to rerender _on every single keypress_ that changed a form value. This was fine for small login forms, but lead to extremely slow performance on larger forms with dozens or hundreds of fields.

> **In `v6`, every single field is connected to the Redux store.** The outer form component is also connected, but is connected in such a manner that does not require it to refresh every time a value changes.

**This helped us realize that most of our issues can be resolved by breaking our big container into small dedicated connected components.** So if we go back to our example we can create a `UserName` connected component:

```jsx
const UserName = ({ userName }) => (
    <React.Fragment>
        {userName}
    </React.Fragment>
);

const mapStateToProps = state => ({
    userName: `${state.user.firstName} ${state.user.lastName}`
});

export default connect(mapStateToProps)(UserName);
```

> _**Note:** If you’re using a react version below 16, you’ll need to wrap the text with a &lt;span/&gt; element 😦. If you’re not familiar with_ React.Fragment _check_ [_the docs_](https://reactjs.org/docs/fragments.html)

And now we can use it anywhere in the app:

```jsx
const UserDropdown = ({ userName }) => (
    <NavDropdown title={<UserName />}>
        <MenuItem>Settings</MenuItem>
        <MenuItem>Logout</MenuItem>
    </NavDropdown>
);
```

```jsx
// or in some other render function somewhere in my code
...
<H1>Hello <UserName />!</H1>
...
```

**Let’s get more flexible with another implementation.**

Now let’s say the **NavDropdown::title** prop can only accept a string and not a react component, we can make our implementation more flexible with the beloved _renderProp_ concept.

```javascript
// our component expects getting children as function
const UserName = ({ children, userName }) => (
   children({ userName })    
);

const mapStateToProps = state => ({
    userName: `${state.user.firstName} ${state.user.lastName}`
});

export default connect(mapStateToProps)(UserName);

// because we still want UserName to return string for most cases we 
// create a component for that

export const UserNameText = () => (
    <UserName>
        {({ userName }) => (
            <React.Fragment>
                {userName}    
            </React.Fragment>
        )}
    </UserName>
);
```

This is pretty straight forward and a quick win that saves some code. As you can see, we’ve implemented a`UserNameText` component that acts like our old `UserName` component.

Refactoring the component is easy, it’s implemented in one place and every change we make will affect all areas in our code that use this component.

Getting the user name from the state is now much much simpler. We just need to import our `UserName` component and render it where ever we want.

**And finally, now only the**`UserName` **component is rendered when our relevant Redux state changes.**

**connect\(\) Optimizations**

Although now we’ve gained some performance benefits and more modular code, these optimizations can still easily come back to bite us in the ass if we are not cautious enough.

Breaking into smaller connected components means you’ll have a lot more connected components rendered in your page, which means you’ll have a lot more `mapStateToProps` functions that will be called. React-Redux listens to **every** change in the store — on every dispatched action, regardless of what `mapStateToProps` returns. Meaning you shouldn’t implement any heavy state parsing in this function.

**The** `mapStateToProps` **function is called on every store change, which is the default behavior in the React-Redux library.** Luckily, we can control that by overriding the `areStatesEqual` to ignore state changes except our relevant slice of state.

```javascript
const mapStateToProps = state => ({
    userName: `${state.user.firstName} ${state.user.lastName}`
});

const areStatesEqual = (next, prev) => (
   prev.user === next.user
);

export default connect(mapStateToProps, null, null , {
    areStatesEqual
})(UserName);
```

Then our `mapStateToProps` function will be called only when the user object changes in our store. Note that our reducer implementation should return a new user object on every update, so shallow equaling is enough to know if a change has happened.

Another important point to understand about React-Redux’s connected component implementation is that the library has its own optimizations of when to update and render the component. It implements the `shouldComponentUpdate` lifecycle hook in a way that it shallow compares the output of the `mapStateToProps` function. Much like React’s PureComponent, the difference is that PureComponents compares all of the component’s props.

**Now, with that in mind we can try to avoid creating new objects in the**`mapStateToProps` **function.**

```javascript
// BAD. Unnecessary new object is always created, thus losing
// react-redux optimization
const mapStateToProps = state => ({
    userData: {
        fullName: `${state.user.firstName} ${state.user.lastName}`,
        jobTitle: state.user.jobTitle,
    },
    ...
});

// Instead we should try to keep the props shallow
const mapStateToProps = state => ({
    userFullName:`${state.user.firstName} ${state.user.lastName}`,
    userJobTitle: state.user.jobTitle,
    ...
});
```

As you can see, it’s very easy to cause our component to unintentionally render on every state change although most of the times the state is the same. Sometimes it’s hard to avoid creating new objects or arrays in your function, usually when you need to render an array of objects filtered from the state:

```javascript
// Array.filter creates new array on every mapStateToProps call
const mapStateToProps = state => {
    const activeTodos = state.todos.filter(todo => todo.isActive);
    return {
        activeTodos
    }
};

// It might be better to just pass all of the to-dos and do 
// the filtering in the render function
const mapStateToProps = state => ({
    todos: state.todos
});

render() {
    const activeTodos = this.props.todos.filter(
        todo => todo.isActive
    );

    return (
        <div>
            {activeTodos.map(todo => <TodoItem {...todo} />)}
        </div>
    )
}
```

So what we get here is instead of a render call on every state change regardless if it’s related to the _to-dos_ or not, our component will render only when new array of _to-dos_ is created in the state.

That’s just one simple common scenario. Sometimes you’ll have no choice but to create a new array in your `mapStateToProps` function. In these cases you might just want to implement `shouldComponentUpdate` yourself or use memoized selectors with [reselect](https://github.com/reduxjs/reselect).

Another way you can use is just passing on an id array and then make each `<TodoItem/>` a connected component that receive to-do id as a prop. The small `TodoItem` component will know to select the to-do from the state and if `mapStateToProps` is implemented correctly, a render call will only happen when the relevant to-do is changed. You can refer to the following [Tree View example in the Redux repository](https://github.com/reduxjs/redux/tree/master/examples/tree-view).

#### Conclusion <a id="8321"></a>

As we discovered overtime it’s pretty easy to encounter performance issue with React using Redux in medium to large applications.

The performance problems we’ve encountered were usually related to redundant render cycles in our app and sometimes related to the fact that we have a lot of `mapStateToProps` __computations __going on.

Breaking common state-view logic into small connected components will help you make your UI components bound more concisely to the relevant redux state that they are actually rendering.

**Source**: [https://theblog.workey.co/lessons-learned-using-react-redux-14a1f2b895f5](https://theblog.workey.co/lessons-learned-using-react-redux-14a1f2b895f5)

