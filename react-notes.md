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
        constrcutor(props) {
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

