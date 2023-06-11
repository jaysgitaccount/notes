# React Notes

## `setState()`
`state` is a field that has special meaning in React, along with `props` (which is set up automatically for every component and all constructors should take the parameter `props`).
1. Don't modify state directly. Instead, use `setState()`.

    // Not this
    this.state.comment = 'Hello'

    // This
    this.setState({comment: 'Hello'});

The constructor is the only place where `this.state` can be assigned.

2. State updates may be asynchronous.

React may batch multiple `setState()` calls into a single update. So `this.props` and `this.state` may be updated asynchronously, and you should not rely on their values for calculating the next state.

To fix this, use a second form of `setState()` that accepts a function instead of an object. The function will receive the previous state as the first argument, and the props at the time the update is applied as the second argument.

    // Arrow func eg
    this.setState(state, props) => ({
        counter: state.counter + props.increment
    })

    // Function eg
    this.setState(function(state, props) {
        return {
            counter: state.counter + props.increment
        };
    });

3. State updates are merged.

When `setState()` is called, React merges (shallow merging) the object provided into the current state.

e.g. If you had

    constructor(props) {
        super(props);
        this.state = {
            posts: [],
            comments: []
        };
    }

You can update them independently with separate setState calls.

    componentDidMount() {
        fetchPosts().then(response => {
            this.setState({
                posts: response.posts
            });
        });

        fetchComments().then(response => {
            this.setState({
                comments: response.comments
            });
        });
    }

### Data flows down

State is often called local or encapsulated, because it is not accessible outside of the component it belongs to.

But a component can pass its state down as props to its child components, e.g `<FormattedDate date={this.state.date} />` From this example, in the child component:

    function FormattedDate(props) {
        return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
    }

This is called "top-down"/"unidirectional" data flow.

## Events
Syntax: in React, events are named using camelCase, e.g. `onClick`. Also, in JSX you pass a function as the event handler instead of a string.

    <button onClick={petDog}>
        Pet the dog
    </button>

You must also explicitly call `preventDefault` if you wish to do that.

In React, you don't need to `addEventListener`. Instead, provide a listener when the element is initially rendered.

In classes, it's common to store the event handler as a method on the class:

    class Toggle extends React.Component {
        constructor(props) {
            super(props);
            this.state = {isToggleOn: true};

            // Bind `this` so it works in the callback
            this.handleClick = this.handleClick.bind(this);
        }

        handleClick() {
            this.setState(prevState => ({
                isToggleOn: !prevState.isToggleOn
            }));
        }

        render() {
            return (
                <button onClick={this.handleClick}>
                    {this.state.isToggleOn ? 'ON' : 'OFF'}
                </button>
            )
        }
    }

As always, be careful when using `this`. Generally when referring to a method but not invoking it with `()`, you should think about binding that method, or using **public class fields syntax**, which is an arrow function:

    class LoggingButton extends React.Component {
        // This syntax ensures `this` is bound within handleClick.
        handleClick = () => {
            console.log('this is:', this);
        };
        render() {
            return (
            <button onClick={this.handleClick}>
                Click me
            </button>
            );
        }
    }

Or, use an arrow function in the callback:

    render() {
        // This syntax ensures `this` is bound within handleClick
        return (
            <button onClick={() => this.handleClick()}>
                Click me
            </button>
        );
    }

However, **this creates an issue where a different callback is created each time `LoggingButton` renders**. This could eventually lead to memory leak. It's better to use public class fields syntax or bind `this` instead.

### Passing Arguments to Event Handlers

If `id` is row ID, either of these would work:

    // Arrow function
    <button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>

    // Function.prototype.bind
    <button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>

In both cases `e` is passed as a second argument, after ID. However, with the arrow function, you need to explicitly pass it. With bind, any other arguments are automatically forwarded.

## Conditional Rendering
Much like in JS, you can use `if` or conditional operators to select which components to display.

e.g. Have these 2 components:

    function UserGreeting(props) {
        return <h1>Welcome back!</h1>;
    }

    function GuestGreeting(props) {
        return <h1>Please sign up.</h1>;
    }

And a Greeting component:

    function greeting(props) {
        const isLoggedIn = props.isLoggedIn;
        if (isLoggedIn) {
            return <UserGreeting />;
        }
        return <GuestGreeting />
    }

    // Rendering it
    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<Greeting isLoggedIn={false} />);

## Element variables
You can use variables to store elements so you can conditionally render a part of the component while the rest of the output doesn't change.

e.g. With these 2 components:

    function LoginButton(props) {
        return (
            <button onClick={props.onClick}>
            Login
            </button>
        );
    }

    function LogoutButton(props) {
        return (
            <button onClick={props.onClick}>
            Logout
            </button>
        );
    }

Then create a stateful component called LoginControl that renders either component depending on its state (and the greeting from the previous example).

    class LoginControl extends React.Component {
        constructor(props) {
            super(props) {
            this.handleLoginClick = this.handleLoginClick.bind(this);
            this.handleLogoutClick = this.handleLogoutClick.bind(this);
            this.state = {isLoggedIn: false};
            }
        }

        handleLoginClick() {
            this.setState({isLoggedIn: true});
        }

        handleLogoutClick() {
            this.setState({isLoggedIn: false});
        }

        render() {
            const isLoggedIn = this.state.isLoggedIn;
            let button;
            if (isLoggedIn) {
                button = <LogoutButton onClick={this.handleLogoutClick} />;
            } else {
                button = <LoginButton onClick={this.handleLoginClick} />;
            }

            return (
                <div>
                    <Greeting isLoggedIn={isLoggedIn} />
                    {button}
                </div>
            )
        }
    }

So `render` will swap out the `{button}` that is returned to the correct version.

You can use `if` to conditionally render, but you may want to use a shorter syntax as well.

#### Inline If with &&
You can embed expressions in JSX by wrapping them with `{}`, including `&&`. So you can use this to conditionally include an element:

    function Mailbox(props) {
        const unreadMessages = props.unreadMessages;
        return (
            <div>
                <h1>
                {unreadMessages.length > 0 &&
                    <h2>
                        You have {unreadMessages.length} unread messages.
                    </h2>
                }
                </h1>
            </div>
        )
    }

This works because in JS, **true `&&` expressions always evaluate to that expression, and false `&&` expressions evaluate to `false`**.

So if the expression is true, the element after `&&` will appear, and if not, it is skipped.

**NOTE**: returning a falsy expression will cause the element after `&&` to be skipped, but the falsy expression will still be returned! In the below example, `<div>0</div>` will be returned.

    render() {
        const count = 0;
        return (
            <div>
                {count && <h1>Messages: {count}</h1>}
            </div>
        );
    }

#### Inline if-else with ternary operator
e.g. Conditionally rendering a small block of text

    render() {
        const isLoggedIn = this.state.isLoggedIn;
        return (
            <div>
                The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
            </div>
        )
    }

Can be used for larger expressions (a bit less readable):

    render() {
        const isLoggedIn = this.state.isLoggedIn;
        return (
            <div>
                {isLoggedIn
                    ? <LogoutButton onClick={this.handleLogoutClick} />
                    : <LoginButton onClick={this.handleLoginClick} />
                }
            </div>
        )
    }

#### Prevent Component from rendering
If (in rare cases) you want a component to hide itself even though it was rendered by another component, return `null` instead of its render output.

    function WarningBanner(props) {
        if (!props.warn) {
            return null;
        }

        return (
            <div className="warning">
            Warning!
            </div>
        );
    }

Returning `null` this way does not affect the firing of the component's lifecycles.

## Lists and Keys
In below e.g., we use `map()` to double an array of numbers, assigning the returned array to `doubled`:

    const numbers = [1, 2, 3, 4, 5];
    const doubled = numbers.map((number) => number * 2);
    console.log(doubled);

In React, transforming arrays into lists of elements is nearly identical.

### Rendering multiple components
You can build collections of elements and include them in JSX using `{}`.

Just like above, we're looping through the array and returning a `<li>` for each item, then assigning the elements to `listItems`:

    const numbers = [1, 2, 3, 4, 5];
    const listItems = numbers.map((number) => 
        <li>{number}</li>
    );

Then, include the entire `listItems` inside a `<ul>` element:

    <ul>{listItems}</ul>

### Basic list component
Usually you render lists inside a component.

Let's refactor the previous example (which is hard coded) into a component that accepts an array of numbers and output a list of elements:

    function NumberList(props) {
        const numbers = props.numbers;
        const listItems = numbers.map((number) => 
            <li>{number}</li>
        );
        return {
            <ul>{listItems}</ul>
        };
    }

    const numbers = [1, 2, 3, 4, 5];
    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<NumberList number={numbers} />);

When this code is ran, you'll see a warning that a **key** should be provided for list items. A key is a special string attribute you need to include when creating lists of elements.

Assigning a key to our list items inside `numbers.map()`:

    function NumberList(props) {
        const numbers = props.numbers;
        const listItems = numbers.map((number) => 
            <li key={number.toString()}>
                {number}
            </li>
        );
        return {
            <ul>{listItems}</ul>
        };
    }

### Keys
Keys help React identify which items were changed/added/removed. Keys should be given to the elements inside the array to give the elements a stable identity:

    const numbers = [1, 2, 3, 4, 5];
    const listItems = numbers.map((number) =>
        <li key={number.toString()}>
            {number}
        </li>
    );

The best way to pick a key is to use a string that is unique to that list item within the list. Most often, this is IDs from your data:

    const todoItems = todos.map((todo) =>
        <li key={todo.id}>
            {todo.text}
        </li>
    );

If you don't have a stable ID, you can use the item index as a key (last resort):

    const todoItems = todos.map((todo, index) =>
        // Only do this if items have no stable IDs
        <li key={index}>
            {todo.text}
        </li>
    );

Using indexes for keys is *not recommended* if the order of items may change. This can negatively impact performance and cause issues with component state. It's better to try to assign unique keys.

### Extracting components with keys
Keys only make sense in the context of the surrounding array.

If you extract a `ListItem` component, you should keep the key on the `<ListItem />` elements in the array, instead of on the `<li>` element in the `ListItem` itself.

Example of incorrect key usage:

    function ListItem(props) {
        const value = props.value;
        return (
            // Wrong! There is no need to specify the key here:
            <li key={value.toString()}>
            {value}
            </li>
        );
    }

    function NumberList(props) {
        const numbers = props.numbers;
        const listItems = numbers.map((number) =>
            // Wrong! The key should have been specified here:
            <ListItem value={number} />
        );
        return (
            <ul>
            {listItems}
            </ul>
        );
    }

Instead, do this:

    function NumberList(props) {
        const numbers = props.numbers;
        const listItems = numbers.map((number) =>
            // Correct! Key should be specified inside the array.
            <ListItem key={number.toString()} value={number} />
        );
        return (
            <ul>
            {listItems}
            </ul>
        );
    }

It seems like you should be setting keys at the part of the code where you are actually creating the parent list using the `ListItem`, instead of building it into the `ListItem` itself.

Good rule of thumb: all elements inside the `map()` call need keys.

### Keys only need to be unique among siblings

They don't need to be globally unique.

They serve as hints to React but don't get passed to your components. If you need the same value in your component, pass it explicitly as a prop with a different name:

    const content = posts.map( post =>
        <Post
            key={post.id}
            id={post.id}
            title={post.title} />
    );

In above, the Post component can read the `id` but not `key`.

### Embedding map() in JSX
JSX allows embedding any expression in curly brackets, so we could refactor the above `NumberList` example to:

    function NumberList(props) {
        const numbers = props.numbers;
        return (
            <ul>
                {numbers.map( number => 
                    <ListItem key={number.toString()}
                              value={number} />
                )}
            </ul>
        )
    }

Instead of creating a separate `listItems` expression, you can embed it as part of the return statement.

**NOTE**: As always, keep in mind readability when doing this. Sometimes it is simply better to split up your code.

## Forms
In React, form elements naturally keep some internal state.

e.g. this HTML form accepts a name:

    <form>
        <label>
            Name:
            <input type="text" name="name" />
        </label>
        <input type="submit" value="Submit" />
    </form>

This form has the default behaviour of browsing to a new page when the user submits the form, which it also does in React. But, if you want to have a JS function that handles submitted data, the standard way is called controlled components.

### Controlled Components
In HTML, form controls like input, textarea, select maintain their own state and update it based on user input. In React, mutable state is kept in the state property of the components, and only updated in `setState()`.

You can combine these two by making the React state the "single source of truth". Then, the React component that renders the form also controls what happens in the form from user input. An input form element whose value is controlled by react in this way is called a *controlled component*.

e.g If we want to make the above HTML form log the name when submitted, we write the form as a controlled component:

    class NameForm extends React.Component {
        constructor(props) {
            super(props);
            // Set value in the state
            this.state = {value: ''};

            this.handleChange = this.handleChange.bind(this);
            this.handleSubmit = this.handleSubmit.bind(this);
        }

        handleChange(event) {
            // Set the value in the state to match the input
            this.setState({value: event.target.value});
        }

        handleSubmit(event) {
            alert('A name was submitted: ' + this.state.value);
            event.preventDefault();
        }

        render() {
            return(
                <form onSubmit={this.handleSubmit}>
                    <label>
                        Name:
                        <input type="text" value={this.state.value} onChange={this.handleChange} />
                    </label>
                    <input type="submit" value="Submit" />
                </form>
            );
        }
    }

Since the value attribute is set on the form element, the displayed value will always be `this.state.value`, making the React state the source of truth. `handleChange` runs on every keystroke to update the React state, so the displayed value will type as the user types.

Because a controlled component's input value is always driven by React state, you can pass the value to other UI elements too, or reset it from other event handlers.

#### textarea
In HTML a textarea defines its text by its children. In React, a textarea uses a value attribute instead, so a form using a textarea can be written similarly to a form using a single-line input:

    <textarea value={this.state.value} onChange={this.handleChange} />

(including the controlled component code in the rest of the class)

If you want an input/textarea to start with default text, initialise the value of `value` in the constructor:

    constructor(props) {
        super(props);
        this.state = {
            value: 'Please write an essay about your favorite DOM element.'
        };

        this.handleChange = this.handleChange.bind(this);
        this.handleSubmit = this.handleSubmit.bind(this);
    }

#### select
In HTML, `select` creates a dropdown list.

    <select>
        <option value="grapefruit">Grapefruit</option>
        <option value="lime">Lime</option>
        <option selected value="coconut">Coconut</option>
        <option value="mango">Mango</option>
    </select>

Note the `selected` attribute which makes that option selected initially.  React, instead of using `selected`, uses a `value` attribute on the root `select` tag, which is initialized in the constructor.

    class FlavorForm extends React.Component {
        constructor(props) {
            super(props);
            this.state = {value: 'coconut'};

            this.handleChange = this.handleChange.bind(this);
            this.handleSubmit = this.handleSubmit.bind(this);
        }

        handleChange(event) {
            this.setState({value: event.target.value});
        }

        handleSubmit(event) {
            alert('Your favorite flavor is: ' + this.state.value);
            event.preventDefault();
        }

        render() {
            return (
            <form onSubmit={this.handleSubmit}>
                <label>
                Pick your favorite flavor:
                <select value={this.state.value} onChange={this.handleChange}>
                    <option value="grapefruit">Grapefruit</option>
                    <option value="lime">Lime</option>
                    <option value="coconut">Coconut</option>
                    <option value="mango">Mango</option>
                </select>
                </label>
                <input type="submit" value="Submit" />
            </form>
            );
        }
    }

As above, when creating the select form control, you can then set the value just like textarea and input.

The controlled component technique means that inputs, textareas and selects all work very similarly.

**NOTE**: You can pass an array into the `value` attribute to select multiple options in a select tag.

    <select multiple={true} value={['B', 'C']}>

I don't think I'll finish taking notes on each form control in React just yet. I'll come back to this later.

## Lifting State Up
Often, several components need to reflect the same changing data. It's recommended to "lift" the shared state up to their closest common ancestor.

Let's see this by creating a temperature calculator.

## Composition vs Inheritance
Apparently, React has a powerful composition model. Let's look at some issues that may make people go for inheritance when they should use composition instead?

### Containment
Components don't knowing their children ahead of time, e.g. `Sidebar` or `Dialog` (generic boxes).

For this, use the special `children` prop to pass children elements directly to their output:

    function FancyBorder(props) {
        return (
            <div className={'FancyBorder FancyBorder-' + props.color}>
                {props.children}
            </div>
        );
    }

This lets other components pass arbitrary children to them by nesting the JSX:

    function WelcomeDialog() {
        return (
            <FancyBorder color='blue'>
                <h1 className="Dialog-title">
                    Welcome
                </h1>
                <p className="Dialog-message">
                    Thank you for visiting our spacecraft!
                </p>
            </FancyBorder>
        );
    }

I assume that `h1` and `p` are the `props.children` that are set by `WelcomeDialog`. Anything within the `FancyBorder` tag gets passed as a `children` prop, which is rendered within a div.

If you need multiple "slots" for children, you can make up your own conventions, you don't have to use `children`:

    function SplitPane(props) {
        return (
            <div className="SplitPane">
                <div className="SplitPane-left">
                    {props.left}
                </div>
                <div className="SplitPane-right">
                    {props.right}
                </div>
            </div>
        );
    }

    function App() {
        return (
            <SplitPane
                left={
                    <Contacts />
                }
                right={
                    <Chat />
                } />
        )
    }

React elements like `<Contacts />` and `<Chat />` are just objects, you can pass them as props like any other data.

### Specialization
Sometimes, a component is a "special case" of another component, e.g. `WelcomeDialog` being a special case of `Dialog`.

In React, this is achieved by composition, where a more "specific" component renders a more "generic" one and configures it with props:

    function Dialog(props) {
        return (
            <FancyBorder color="blue">
                <h1 className="Dialog-title">
                    {props.title}
                </h1>
                <p className="Dialog-message">
                    {props.message}
                </p>
            </FancyBorder>
        );
    }

    function WelcomeDialog() {
        return (
            <Dialog
                title="Welcome"
                message="Thanks for visiting!" />
        )
    }

This also applies to class components:

    function Dialog(props) {
        return (
            <FancyBorder color="blue">
                <h1 className="Dialog-title">
                    {props.title}
                </h1>
                <p className="Dialog-message">
                    {props.message}
                </p>
                {props.children}
            </FancyBorder>
        );
    }

    class SignUpDialog extends React.Component {
        constructor(props) {
            super(props);
            this.handleChange = this.handleChange.bind(this);
            this.handleSignUp = this.handleSignUp.bind(this);
            this.state = {login: ''};
        }

        render() {
            return (
                <Dialog title="Mars Exploration Program">
                        message="How should we refer to you?">
                    <input value={this.state.login}
                           onChange={this.handleChange} />
                    <button onClick={this.handleSignUp}>
                        Sign me up!
                    </button>
                </Dialog>
            );
        }

        handleChange(e) {
            this.setState({login: e.target.value});
        }

        handleSignUp() {
            alert(`Welcome aboard, ${this.state.login}!`);
        }
    }

### What about inheritance?
Based on what I'm reading, the React documentation says that you should not create component inheritance hierarchies. Props and composition give you all the flexibility you need to customize a component's look and behaviour in an explicit and safe way.

Remember, components may accept arbitrary props, including primitive values, React elements, or functions.

If you want to reuse non-UI functionality between components, they recommend extracting it into a separate JavaScript module, for components to import and use that function, object or class without extending it.

Basically, think about components via what they *do*, not what they *are*.

## Thinking in React
Thought process of building a searchable product data table using React:

### Start with a mock
Imagine we have a JSON API and a designer mockup.

### 1. Break UI into component hierarchies
Draw boxes around every component and subcomponent in the mockup image and name them.

To decide how to split up components, use the same technique for deciding if you should create a new function or object, like the *single responsibility principle* - a component should ideally only do one thing. If it ends up growing, decompose it into smaller subcomponents.

UI and data tend to be adhere to the same information structure.

After you've identified the components, arrange them into a hierarchy. e.g.:

- FilterableProductTable
    - SearchBar
    - ProductTable
        - ProductCategoryRow
        - ProductRow

### 2. Build a static version in React.
As in, build a version that takes the data model and renders the UI but has NO interactivity. You should decouple these processes because building a static version requires much typing and not much thinking, whereas adding interactivity requires a lot of thinking and not a lot of typing.

To do this, build components that reuse other components and pass data using *props*. Don't use state AT ALL in this static version. State is reserved for interactivity (data that changes over time).

You can build top-down or bottom-up. In smaller projects it's usually easier to go top-down, and in larger projects it's easier to go bottom-up and write tests as you build.

At the end of this step, you'll have a library of reusable components that render your data model. The components will only have `render()` methods as this is a static version of the model.

The component at the top of the hierarchy (FilterableProductTable in the example) will take your data model as a prop, so if you make a change to the underlying data model and call `root.render()` again, the UI will update. That way, you can see how the UI is updated and where to make changes.

React's *one-way data flow* (aka 'one-way binding') keeps things modular and fast.

### 3. Identify the minimal (but complete) representation of UI state
Using the concept of DRY (don't repeat yourself), figure out the absolute minimal representation of the state that your application needs, and compute everything else needed on-demand.

e.g If building a todo list, keep an array of the todo items around, DON'T keep a separate state variable for the count. Instead, when you want to render the todo count, take the length of the todo items array.

For each piece of data in your example app, ask these questions:
1. Is it passed in from a parent via props?
2. Does it remain unchanged over time?
3. Can you compute it based on any other state or props in your component?

If the answer to any of these is yes, then this data probably doesn't need to be stored in state.

### 4. Identify where the state should live
Now you need to identify which component mutates/owns this state.

Because React's data flow is one-way, it may not immediately be clear which component should own what state. To figure it out, for each piece of state in your application:

- Identify every component that renders something based on that state
- Find a common owner component (above all those components in the hierarchy)
- Either the common owner or another component higher up in the hierarchy should own the state.
- If you can't find a component where it makes sense to own the state, create a new component solely for holding the state and add it somewhere in the hierarchy above the common owner component.

After determining this, then add an instance property to the owner component. In the example above, it would be, in `FilterableProductTable`, `this.state = {filterText: '', inStockOnly: false}` to `FilterableProductTable`'s `constructor` to reflect the initial state of the application. Then, pass `filterText` and `inStockOnly` to `ProductTable` and `SearchBar` as a prop. Then, in those components, use the props to filter rows/set form values.

### Add inverse data flow
After setting up props/states flowing down the hierarchy, set up data flowing the other way, e.g. form components deep in the hierarchy need to update the state further up, in `FilterableProductTable`.

Because React only does top-down data flow and each component's state should only be controlled by that component, you should probably do the controlled component setup. 

This means that you've set up the `value` prop of the `input` to ALWAYS be equal to the `state` that you passed in from `FilterableProductTable`, probably like:

    <Searchbar value={this.state.filterText} />

For this example, using the `onChange` event to detect user input on input fields, you should pass callbacks from `FilterableProductTable` to `SearchBar` (as props) that will fire whenever the state should be updated. Within `FilterableProductTable`, the callback will fire `setState()`, stored within a method on the component. The callback will be receiving the value of the input, which is obtained by:

    function Searchbar(props) {
        handleChange(e) {
            this.props.onTypingInSearchBar(e.target.value);
        }

        render() {
            return (
                <fieldset>
                    <legend>Search</legend>
                    <input value={this.props.value}
                            onChange={this.handleChange} />
                </fieldset>
            )
        }
    }

The callback name, of course, would come from `FilterableProductTable`:

    <Searchbar
        value={this.state.filterText}
        ontypingInSearchBar={this.myCallBack} />

## setState

Don't call setState in a constructor method; instead, assign the initial state to `this.state` directly in the constructor.

Avoid copying props into state! It's unnecessary (you can just use `props.` instead) and it creates bugs (updates to the prop won't be reflected in the state.) **Only do this if you intentionally want to ignore prop updates.**

`setState` asynchronously enqueues updates to the state and tells React that this component and its children need to be re-rendered to reflect the update. It is the primary method used to update the user interface in response to event handlers and server responses.

`setState` will always lead to a re-render unless `shouldComponentUpdate()` returns `false`. If mutable objects are being used  and conditional rendering logic cannot be implemented in `shouldComponentUpdate()`, calling `setState` only when the new state differs from the previous state will avoid unnecessary re-renders.

In `setState(updater[, callback])`:

The first argument is an **updater** function with the signature `(state, props) => stateChange`, where state is a reference to the component state at the time the change is applied. It should not be directly mutated; instead, build a new object based on the input from `state` and `props`.

e.g. if we wanted to increment a value in state by `props.step`:

    this.setState((state, props) => {
        return {counter: state.counter + props.step};
    })

This is the **updater** function form, where you feed the previous state value into the function to ensure it executes with the latest version of data.

The second parameter is an optional callback that will be executed once `setState` completes and the component re-renders. Generally it's recommended to use `componentDidUpdate()` instead.

## Fragments
Syntax: `<></>` (this is shorthand)
By wrapping JSX in fragment tags, you can get set up conditional rendering outside of the `return` or `render` method.

## Refs and Forwarding Refs
In React, you don't have access to direct DOM queries like in vanilla JS. You instead store `refs` which are direct references to HTML DOM elements.

In functional components, you initialize the ref using `useRef(null)`. Then you assign an element with that ref, like an attribute `ref={refName}` when you write JSX elements.

In React, data flows "top-down". If I wanted to store a reference to a child component, and use that ref in the parent, I guess I would just have to assign the ref in the parent instead of forwarding it to the child?

From [here](https://stackoverflow.com/questions/54633690/how-can-i-use-multiple-refs-for-an-array-of-elements-with-hooks), turns out you can do `const itemsRef = useRef([])` (declare an array of refs). [BUT NOT WITH A FUNCTIONAL COMPONENT](https://stackoverflow.com/questions/66664209/how-can-i-use-forwardref-in-react). You have to do a "forwardRef callback" for that.

I did this:

    const DisplayRef = forwardRef((props, ref) => {
        return (
            <div className='Display' ref={ref}>
                <Display {...props} />
            </div>
        )
    })

    const AddDisplayRef = forwardRef((props, ref) => {
        return (
            <div className='Display' ref={ref}>
                <AddDisplay {...props} />
            </div>
        )
    })

Basically, I wrapped the children of these components in a fragment instead of a `div`, then applied the ref directly to the original parent `div`, which is now declared inside `forwardRef`.

I probably could have made the ref accessible within the component by passing it to a child DOM element instead, but I didn't need to do that for this use case.

I assume React automatically tracks which item gets deleted and updates the array for me, because it worked right off the bat with this code.

Okay well apparently you can just do this

    import React, {useRef, forwardRef} from 'react';
    //DO THIS
    export function ParentComponent() {
        const nodeRef = useRef(null);
        return (
        <ChildComponent ref={nodeRef}
        )
    };
    export function ChildComponent forwardRef((props, ref) {
        return (
            <div ref={ref}>
            <p>Pass refs like this</p>
            </div>
        )
    });

## props.children

You can decouple a "container" component from any child components it will receive as props using `props.children`.

    return (
        <div>
            <Picture key={picture.id} src={picture.src}>
                // what is placed here is passed as props.children
            </Picture>
        </div>
    )

    const Picture = (props) => {
        return (
            <div>
                <img src={props.src}/>
                {props.children}
            </div>
        )
    }

## More refs
`createRef()` mounts/unmounts with the parent component. It doesn't respond to a child being added or removed. A ref callback provides a safe reference between parent and chid by storing the child node in the parent ref property.

Ref callbacks that are defined as inline functions get called TWICE during updates, and the first one returns `null`, because a new instance of the function is created with each render, so React will clear up the old ref and set up the new one. To bypass this, do a check like `if (typeof element === "object")...`

OR, do [this](https://elfi-y.medium.com/react-callback-refs-a-4bd2da317269#:~:text=You%20can%20pass%20callback%20refs,createRef()%20.&text=The%20HigherComponent%20passes%20its%20ref,attribute%20to%20the%20.)

Ref callbacks are SUPPOSED to execute on mount