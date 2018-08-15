# Clean Code vs. Dirty Code: React Best Practices

his article will focus on clean code practices as they apply to modern React software development. I’ll also talk about some of the “sugar” that ES6/ES2015 brings to the table.

### What is clean code, and why do I care? {#what-is-clean-code-and-why-do-i-care}

Clean code is a consistent style of programming that makes your code easier to write, read, and maintain. Often a developer spends time on a problem, and once the problem is solved, they make a pull request. I contend that you aren’t done just because your code “works.”

Now is your chance to clean it up by removing dead code \(zombie code\), refactoring, and removing any commented-out code! Strive for maintainability. Ask yourself, “Will someone else be able to understand this code six months from now?”

In simpler terms, write code that you would be proud to take home and show your mother.

Why do you care? Because if you’re a good developer, you’re lazy. Hear me out – I mean that as a compliment. A good developer, when faced with a situation where they have to do something more than once, will generally find an automated \(or better\) solution to complete the task at hand. So because you’re lazy, subscribing to clean-code techniques will decrease the frequency of changes from pull-request code reviews and the need to come back to the same piece of code over and over.

#### Clean code passes the “smell test” {#clean-code-passes-the-smell-test}

Clean code should pass the smell test. What do I mean by that? We’ve all looked at code \(our own or others’\) and said, “Something’s not quite right here.” Remember, if it doesn’t feel right, it probably isn’t. Code that’s well thought out just comes together. If it feels like you’re trying to fit a square peg into a round hole, then pause, step back, and take a break. Nine times out of 10, you’ll come up with a better solution.

#### Clean code is DRY {#clean-code-is-dry}

DRY is an acronym that stands for “Don’t Repeat Yourself.” If you are doing the same thing in multiple places, consolidate the duplicate code. If you see patterns in your code, that is an indication it is prime for DRYing. Sometimes this means standing back from the screen until you can’t read the text and literally looking for patterns.

```javascript
// Dirty
const MyComponent = () => (
  <div>
    <OtherComponent type="a" className="colorful" foo={123} bar={456} />
    <OtherComponent type="b" className="colorful" foo={123} bar={456} />    
  </div>
);
```

```javascript
// Clean
const MyOtherComponent = ({ type }) => (
  <OtherComponent type={type} className="colorful" foo={123} bar={456} />
);
const MyComponent = () => (
  <div>
    <MyOtherComponent type="a" />
    <MyOtherComponent type="b" />
  </div>
);
```

Sometimes – as in our example above – DRYing your code may actually increase code size. However, DRYing your code also generally improves maintainability.

Be warned that it’s possible to go _too_ far with DRYing up your code, so know when to say when.

#### Clean code is predictable and testable {#clean-code-is-predictable-and-testable}

Writing unit tests is not just a good idea, it’s become almost mandatory. After all, how can you be sure that your latest shiny new feature didn’t introduce a bug somewhere else?

Many React developers rely on [Jest](https://facebook.github.io/jest/) for a zero-configuration test runner and to produce code coverage reports. And if you’re interested in visual before/after comparison testing, please check out American Express’s own [Jest Image Snapshot](https://github.com/americanexpress/jest-image-snapshot).

#### Clean code is self-commenting {#clean-code-is-self-commenting}

Has this happened to you before? You wrote some code and made sure that it was fully commented. As will happen, you found a bug, so you went back and changed the code. Did you remember to change your comments as well to reflect the new logic? Maybe. Maybe not. The next person who looked at your code then may have gone down a rabbit hole because they focused on the comments.

Add comments only to explain complex thoughts; that is, don’t comment on the obvious. Fewer comments also reduces visual clutter.

```javascript
// Dirty
const fetchUser = (id) => (
  fetch(buildUri`/users/${id}`) // Get User DTO record from REST API
    .then(convertFormat) // Convert to snakeCase
    .then(validateUser) // Make sure the the user is valid
);
```

In the clean version, we rename some of the functions to better describe what they do, thus eliminating the need for comments and reducing visual clutter. This limits the potential confusion of the code not matching the comments later.

```javascript
// Clean
const fetchUser = (id) => (
  fetch(buildUri`/users/${id}`)
    .then(snakeToCamelCase)
    .then(validateUser)
);
```

#### Naming things {#naming-things}

In my previous article [Function as Child Components Are an Anti-Pattern](https://americanexpress.io/faccs-are-an-antipattern), I stressed the importance of naming things. We should all give serious thought to variable names, function names, and even filenames.

Here are a few guidelines:

* Boolean variables, or functions that return a boolean value, should start with “is,” “has” or “should.”

  ```javascript
  // Dirty
  const done = current >= goal;
  ```

  ```javascript
  // Clean
  const isComplete = current >= goal;
  ```

* Functions should be named for what they do, not how they do it. In other words, don’t expose details of the implementation in the name. Why? Because how you do it may change some day, and you shouldn’t need to refactor your consuming code because of it. For example, you may load your config from a REST API today, but you may decide to bake it into the JavaScript tomorrow.

  ```javascript
  // Dirty
  const loadConfigFromServer = () => {
    ...
  };
  ```

  ```javascript
  // Clean
  const loadConfig = () => {
    ...
  };
  ```

#### Clean code follows proven design patterns and best practices {#clean-code-follows-proven-design-patterns-and-best-practices}

Computers have been around a long time. Throughout the years, programmers discovered patterns in the way they solved certain problems. These are called design patterns. In other words, there are algorithms that have been proved over time to work, so you should stand on the shoulders of those who preceded you so that you don’t have to make the same mistakes.

Then there are best practices. They are similar to design patterns but broader, not specific to a coding algorithm. They might cover things like, “You should lint your code” or “When writing a library package, include React as a `peerDependency`.”

Here are some best practices to follow when architecting your React applications.

* Use small functions, each with a single responsibility. This is called the single responsibility principle. Ensure that each function does one job and does it well. This could mean breaking up complex components into many smaller ones. This also will lead to better testability.
* Be on the lookout for leaky abstractions. In other words, don’t impose your internal requirements on consumers of your code.
* Follow strict linting rules. This will help you write clean, consistent code.

#### Clean code doesn’t \(necessarily\) take longer to write {#clean-code-doesnt-necessarily-take-longer-to-write}

I hear the argument all the time that writing clean code will slow productivity. That’s a bunch of hooey. Yes, initially you may need to slow down before you can speed up, but eventually your pace will increase as you are writing fewer lines of code.

And don’t discount the “rewrite factor” and time spent fixing comments from code reviews. If you break your code into small modules, each with a single responsibility, it’s likely that you’ll never have to touch most modules again. There is time saved in “write it and forget it.”

### Practical examples of dirty code vs. clean code {#practical-examples-of-dirty-code-vs-clean-code}

#### DRY up this code {#dry-up-this-code}

Take a look at the code sample below. Go ahead and step back from your monitor as I described above. Do you see any patterns? Notice that the component `Thingie` is identical to `ThingieWithTitle` with the exception of the `Title` component. This is a perfect candidate for DRYing.

```javascript
// Dirty
import Title from './Title';
export const Thingie = ({ description }) => (
  <div class="thingie">
    <div class="description-wrapper">
      <Description value={description} />
    </div>
  </div>
);
export const ThingieWithTitle = ({ title, description }) => (
  <div>
    <Title value={title} />
    <div class="description-wrapper">
      <Description value={description} />
    </div>
  </div>
);
```

Here we’ve allowed the passing of `children` to `Thingie`. We’ve then created `ThingieWithTitle` that wraps `Thingie`, passing in the `Title` as its children.

```javascript
// Clean
import Title from './Title';
export const Thingie = ({ description, children }) => (
  <div class="thingie">
    {children}
    <div class="description-wrapper">
      <Description value={description} />
    </div>
  </div>
);
export const ThingieWithTitle = ({ title, ...others }) => (
  <Thingie {...others}>
    <Title value={title} />
  </Thingie>
);
```

#### Default values {#default-values}

Take a look at the following code snippet. It defaults the `className` to “icon-large” using a logical OR statement, similar to the way your grandfather might have done it.

```javascript
// Dirty
const Icon = ({ className, onClick }) => {
  const additionalClasses = className || 'icon-large';
  return (
    <span
      className={`icon-hover ${additionalClasses}`}
      onClick={onClick}>
    </span>
  );
};
```

Here we use ES6’s default syntax to replace `undefined` values with empty strings. This allows us to use ES6’s single statement form of the fat-arrow function, thus eliminating the need for the `return` statement.

```javascript
// Clean
const Icon = ({ className = 'icon-large', onClick }) => (
  <span className={`icon-hover ${className}`} onClick={onClick} />
);
```

In this even cleaner version, the default values are set in React.

```javascript
// Cleaner
const Icon = ({ className, onClick }) => (
  <span className={`icon-hover ${className}`} onClick={onClick} />
);
Icon.defaultProps = {
  className: 'icon-large',
};
```

Why is this cleaner? And is it really better? Don’t all three versions do the same thing? For the most part, yes. The advantage of letting React set your prop defaults, however, is that it produces more efficient code, defaults props in a `Class` based lifecycle component, as well as allows your default values to be checked against `propTypes`. But there is one more advantage: it declutters the default logic from that of the component itself.

For example, you _could_ do the following, storing all of your default props in one place. I’m not suggesting that you do; I’m just saying that you have the flexibility to do so.

```javascript
import defaultProps from './defaultProps';
...
Icon.defaultProps = defaultProps.Icon;
```

#### Separate stateful aspects from rendering {#separate-stateful-aspects-from-rendering}

Mixing your stateful data-loading logic with your rendering \(or presentation\) logic can lead to component complexity. Instead, write a stateful container component whose single responsibility is to load the data. Then write another component whose sole responsibility is to display the data. This is called the [Container Pattern](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0).

In the example below, user data is loading and is displayed in a single component.

```javascript
// Dirty
class User extends Component {
  state = { loading: true };

  render() {
    const { loading, user } = this.state;
    return loading
      ? <div>Loading...</div>
      : <div>
          <div>
            First name: {user.firstName}
          </div>
          <div>
            First name: {user.lastName}
          </div>
          ...
        </div>;
  }

  componentDidMount() {
    fetchUser(this.props.id)
      .then((user) => { this.setState({ loading: false, user })})
  }
}
```

In the clean version, the concerns – loading data, displaying a loading spinner, and displaying data – have been separated. Not only does this make the code easier to understand, but testing will require a lot less effort as you can test each concern independently. And because `RenderUser` is a stateless functional component, the results are predictable.

```javascript
// Clean
import RenderUser from './RenderUser';
class User extends Component {
  state = { loading: true };

  render() {
    const { loading, user } = this.state;
    return loading ? <Loading /> : <RenderUser user={user} />;
  }

  componentDidMount() {
    fetchUser(this.props.id)
      .then(user => { this.setState({ loading: false, user })})
  }
}
```

#### Use stateless functional components {#use-stateless-functional-components}

Stateless functional components \(SFCs\) were introduced in React v0.14.0, and they are used to greatly simplify a render-only component. But some developers haven’t let go of the past. For example, the following component is ripe for converting to an SFC.

```javascript
// Dirty
class TableRowWrapper extends Component {
  render() {
    return (
      <tr>
        {this.props.children}
      </tr>
    );
  }
}
```

The clean version clears a lot of the screen clutter of the dirty version. Through optimization of React’s core, it’s possible to use far less memory, as no instance is created.

```javascript
// Clean
const TableRowWrapper = ({ children }) => (
  <tr>
    {children}
  </tr>
);
```

#### Rest/spread {#restspread}

About a year ago, it was my belief that `Object.assign` would become everyone’s new best friend. Well times have changed. Enter the [rest/spread spec](https://github.com/tc39/proposal-object-rest-spread) in ES2016/ES7.

Take the case where you pass some props to a component. You’d like to use `className` in the component itself, but pass all other props down the chain. You would do something like this.

```javascript
// Dirty
const MyComponent = (props) => {
  const others = Object.assign({}, props);
  delete others.className;
  return (
    <div className={props.className}>
      {React.createElement(MyOtherComponent, others)}
    </div>
  );
};
```

Not a very elegant solution, is it? But with rest/spread, it’s a piece of cake!

```javascript
// Clean
const MyComponent = ({ className, ...others }) => (
  <div className={className}>
    <MyOtherComponent {...others} />
  </div>
);
```

We take the “rest” of the properties and we “spread” them as new props to `MyOtherComponent`. \(Sometimes things just name themselves…\)

#### Destructure when applicable {#destructure-when-applicable}

ES6 introduced the concept of [destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment), which really _is_ your best friend. Destructuring allows you to “pull apart” properties of an object or elements of an array.

**Object destructuring**

In this example, `componentWillReceiveProps` is passed `newProps`, and we set `state.active` to the new `active` prop.

```javascript
// Dirty
componentWillReceiveProps(newProps) {
  this.setState({
    active: newProps.active
  });
}
```

In this clean version, we destructure `newProps` into `active`. Not only do we no longer need to reference `newProps.active`, but we also can use ES6 object property shorthand in `setState`.

```javascript
// Clean
componentWillReceiveProps({ active }) {
  this.setState({ active });
}
```

**Array destructuring**

An often overlooked ES6 feature is array destructuring. Take the following code for example. It takes in a `locale` such as “en-US” and breaks it into`language` \(en\) and `country` \(US\).

```javascript
// Dirty
const splitLocale = locale.split('-');
const language = splitLocale[0];
const country = splitLocale[1];
```

In the clean version, ES6 has you covered.

```javascript
// Clean
const [language, country] = locale.split('-');
```

### In conclusion… {#in-conclusion}

I hope that I’ve helped you see the benefits of writing clean code and that you can even use some of the practical examples presented here. Once you embrace writing clean code, it will become second nature. You \(and your future self\) will soon appreciate the “write it and forget it” way of life.

**Source**: [https://americanexpress.io/clean-code-dirty-code/](https://americanexpress.io/clean-code-dirty-code/)

