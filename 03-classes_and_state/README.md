# Surpass With Class

We now know how to create React components that can dynamically render elements to the DOM based on props. But how do we
 add actual interactivity? For that we need to use classes to create our components.

### Class syntax

Our simple `Title` component looks like this as a class:

```jsx
class Title extends React.Component {
    
    render() {
        return <h1>{this.props.text}</h1>;
    }
    
}
```

We _extend_ the React base component (which provides us with the methods we'll be using). The only mandatory part of a 
class-based component is the render method. This is effectively the same as the functional components we've been using:
 you need to return a React element.

The other difference you may have spotted is the reference to `this`. Classes may receive props like all components, but
 they access them with the `this` keyword. You can only access arguments passed to an ES6 class in the constructor, so
  React binds them to the instance for you, which is why you have to use `this.props` to reference them.

So if we use JSX to create a React Element, it results in the element object we saw in part 1. But if we initialize the 
class by calling its constructor, we can see how React represents it internally. We get an object with the relevant
 properties we added to the class (and some that we aren't using yet, like refs).

### State

To add interactivity, we need to learn a new concept: __state__. React allows components to keep track of their state as
 an object, which __you can update__ as the result of user interaction. The state object is flexible - you decide what 
 data you want to store in it. By rendering our UI based on this state we can tell React how the page should look and 
 let it worry about actually updating the DOM. This is the radical difference between React and traditional JavaScript:
here we can _describe_ how our UI should look without having to specify every step along the way to updating it.

#### Creating state

We'll worry about the render method in a moment. First, let's look at how to give state to a component:

```jsx
class Toggle extends React.Component {
    
    constructor(props) {
        super(props)
        this.state = {
            toggled: false
        };
    }
    
    render() {...}
    
}
```

We have a constructor (which takes `props` as an argument if you need to use them when initializing your component). 
Inside the constructor we create a property called `state`, which is an object with whatever key/value pairs we need.

There is a simpler way to do this using the ES7 class properties proposal (which Babel will transpile for us):

```jsx
class Toggle extends React.Component {
    
    state = {
        toggled: false
    }
    
    render() {...}
    
}
```

That's it! We don't need to have a constructor and we'll still be able to access our _state_ and _props_.

#### Updating state

React provides all classes with a `setState` method. We can use this two ways:

1. If we're simply updating the state, we can pass a new state object as the argument.
2. If we're basing the new state on the old state (and maybe the props), we can pass a callback function as argument to
 setState that receives the old state (and optionally props) as argument and returns the new state.

##### Example 1 - passing an object to setState:

```jsx
class Toggle extends React.Component {
    
    state = {
        toggled: false
    }
    
    toggleOn() {
        this.setState({toggled: true});
    }
    
}
```

##### Example 2 - passing a function to setState:

```jsx
class Toggle extends React.Component {
    
    state = {
        toggled: false
    }
    
    toggle() {
        this.setState((prevState, props) => {
            return {toggled: !prevState.toggled};
        });
    }
  
    render() {...}
    
}
```

##### After state updates

It's worth highlighting that `setState` is asynchronous. React will batch these calls up, so you can't rely on state
 being updated immediately. If you need something to only happen after a state update you can pass a callback as a second
  argument to `setState`:

```jsx
class Toggle extends React.Component {
  
    state = {
        toggled: false
    }
  
    toggle() {
        this.setState({toggled: true}, () => {
            // do something relying on the state update in here
        });
    }
    
    render() {...}
    
}
```

### Rendering based on state

Now that we have our state updating, we can use it to describe our UI:

```jsx
class Toggle extends React.Component {
  
    state = {
        toggled: false
    }
  
    toggle() {
        this.setState((prevState, props) => {
            return {toggled: !prevState.toggled};
        })
    }
    
    render() {
        return (
            <div>
                {this.state.toggled ? <p>Hidden toggle-able text</p> : null}
            </div>
        )
    }
    
}
```

We are using a ternary operator to either render one thing or another (here the other thing is nothing) based on the state.

#### Event handlers

You may notice we have no way to actually change the state in the previous example. We need to create a button with a 
click-handler that calls our `toggle` method. You can pass events to React elements as props: they start with 'on' 
and end with the event name (e.g. `onClick`, `onChange`, `onMouseEnter` etc).

```jsx
class Toggle extends React.Component {
    
    state = {
        toggled: false
    }
    
    toggle() {
        this.setState((prevState, props) => {
            return {toggled: !prevState.toggled};
        })
    }
    
    render() {
        return (
            <div>
                <button onClick={this.toggle}>Toggle</button>
                {this.state.toggled ? <p>Hidden toggle-able text</p> : null}
            </div>
        )
    }
    
}
```

Unfortunately this will currently throw an error: `this is undefined`.

Practically what that means is you need to make sure `this` is bound correctly. There are a few different ways of doing
 that, but to keep it simple, we are going to use the new class feature of ES7. We can create an arrow function as a 
 class property in the same way we were adding our `state` before:

```jsx
class Toggle extends React.Component {
    
    state = { toggled: false };
    
    toggle = () => {...};
    
    render() {...}
    
}
```

Because arrow functions always get their context from where they were defined, this `toggle` will have the correct `this`.

So here's our final fully functional toggle component:

```jsx
class Toggle extends React.Component {
  
    state = {
        toggled: false
    }
    
    toggle = () => {
        this.setState((prevState, props) => {
            return { toggled: !prevState.toggled };
        })
    }
    
    render() {
        return (
            <div>
                <button onClick={this.toggle}>Toggle</button>
                {this.state.toggled && <p>Hidden toggle-able text</p>}
            </div>
        )
    }
    
}
```

Can you see how the JSX in our render method _describes_ how we want the UI to look? We don't have to write code telling
the browser _how_ to update anything, we just show how it should look for each state and provide a way for the state to
update.

### Lifecycle Methods

React goes through a whole cycle of stages when rendering a component. We can hook into this with a component's
_lifecycle methods_ to ensure our code runs at the correct time.

We are not going to list every method here because there are quite a few and it can be overwhelming. Actually we'll be 
talking about only one of them for now. (It's easy to look them up in the
 [React component docs](https://reactjs.org/docs/react-component.html) when you think you need to use one.)

The one that's important for us right now is: 

#### `componentDidMount`

This is called right after React mounts your component, so you can do things involving the DOM in here. For example if you 
need to fetch data required for initially rendering the component, you can do so in here:

```jsx
class Profile extends React.Component {

    state = {
        loading: true,
        data: {}
    }
  
    componentDidMount() {
        fetch('https://api.com/user')
            .then(response => response.json())
            .then(data => {
                this.setState({
                    data: data, 
                    loading: false
                })
            })
            .catch(error => console.warn(error))
    }
    
    render() {
        return (
            <div>
                {this.state.loading ? 'Loading...': <img src={this.state.data.imgUrl}} />
            </div>
        )
    }
    
}
```

## Exercise

1. Open `index.html` in your editor and browser. You should see the toggle component.
2. Try to make the button display what it's about to do (e.g. "Toggle on" or "Toggle off").
3. Make your own class-based component with state, which should contain a button and an `<h2>` element, which displays
 a new random number between 0 and 100 after each click.

Next: [Forms in React](../04-forms_and_lists)

## Further Reading

- [State and Lifecycle | React Docs](https://reactjs.org/docs/state-and-lifecycle.html)
- [Handling Events | React Docs](https://reactjs.org/docs/handling-events.html)
- [Conditional Rendering | React Docs](https://reactjs.org/docs/conditional-rendering.html)

## License

Copyright © Progmasters (QTC Kft.), 2018.
All rights reserved. No part or the whole of this Teaching Material (TM) may be reproduced, copied, distributed, publicly performed, disseminated to the public, adapted or transmitted in any form or by any means, including photocopying, recording, or other electronic or mechanical methods, without the prior written permission of QTC Kft. This TM may only be used for the purposes of teaching exclusively by QTC Kft. and studying exclusively by QTC Kft.’s students and for no other purposes by any parties other than QTC Kft.
This TM shall be kept confidential and shall not be made public or made available or disclosed to any unauthorized person.
Any dispute or claim arising out of the breach of these provisions shall be governed by and construed in accordance with the laws of Hungary.
