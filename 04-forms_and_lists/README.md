# Storm the Form

Forms are handled a little bit differently in React. This is because, unlike most HTML elements, forms inputs have their
 own internal state stored in the DOM. Ideally we want a single-source of truth in React. We also want to be able to use 
 the input values without having to go into the DOM and get them.

### Controlled components

Generally we use a technique called "controlled components" to keep our input state in React instead of the DOM. This
 involves maintaining an entry in state for each input your form renders:

```jsx
class Form extends React.Component {
  
    state = {
        input: ''
    }
  
    render() {
        return (
            <form>
                <label htmlFor="name">Name:</label>
                <input type="text" id="name" value={this.state.input} />
                <button type="submit">Submit</button>
            </form>
        );
    }
    
}
```

We set the `value` prop of our `<input />` to be the correct entry in our state. Unfortunately this means the input is
 unusable, as we haven't provided any way for that state to change. This is the difference with controlled
  inputs — everything happens within React/JavaScript. The input is just reflecting our state.

```jsx
class Form extends React.Component {
  
    state = {
        input: '',
    }
        
    handleChange = event => {
        const value = event.target.value;
        this.setState({input: value});
    }
    
    render() {
        return (
            <form>
                <label htmlFor="name">Name:</label>
                <input
                    type="text"
                    id="name"
                    value={this.state.input}
                    onChange={this.handleChange}
                />
                <button type="submit">Submit</button>
            </form>
        );
    }
    
}
```

Now our input has an `onChange` event handler that will set the state to the new value every time the user types into
 the field.

#### Different input types

Certain types of inputs have to be handled slightly differently.

##### `textarea`

The `textarea` usually defines the text inside it via its children. React uses a `value` prop to make it consistent
 with other inputs.

##### `select`

In regular HTML a `<select>` usually contains several `<option>` elements, of which one will have the `selected`
 attribute. This is the option shown in the `<select>` in its unexpanded state. In React, however, this again is
 controlled with a `value` prop on the parent `select`. This makes it easier to control as you only have to update 
 the whole form in one function.

```jsx
<label htmlFor="color">What's your favorite color?</label>
<select
        type="select"
        id="color"
        name="color"
        onChange={this.handleChange}
        value={this.state.color}>
    <option value="red">red</option>
    <option value="green">green</option>
    <option value="blue">blue</option>
</select>
```

##### Checkboxes and radios

These input fields are controlled through the `checked` property instead of the `value`. Radiobuttons also have a `value`
 property, which gets saved into the state (indicating which radiobutton is currently chosen). After that, its `checked`
 property will be set based on that value in the state. `checked` can either be true or false.  
 
 It's worth remembering that groups of radios should only be able to have a single option selected, so you should have
 a single entry in state for each group:

```jsx
class Form extends React.Component {
    
    state = {
        radioSelected: 'phone',
    }
    
    handleChange = event => {
        const value = event.target.value;
        this.setState({radioSelected: value});
    }
    
    render() {
        return (
            <form>
                <label htmlFor="phone">Phone</label>
                <input
                    type="radio"
                    id="phone"
                    name="contact"
                    value="phone"
                    checked={this.state.radioSelected === 'phone'}
                    onChange={this.handleChange}
                />

                <label htmlFor="email">Email</label>
                <input
                    type="radio"
                    id="email"
                    name="contact"
                    value="email"
                    checked={this.state.radioSelected === 'email'}
                    onChange={this.handleChange}
                />

                <label htmlFor="post">Post</label>
                <input
                    type="radio"
                    id="post"
                    name="contact"
                    value="post"
                    checked={this.state.radioSelected === 'post'}
                    onChange={this.handleChange}
                />

                <button type="submit">Submit</button>
            </form>
        )
    }
    
}
```

#### Multiple inputs

If we have more than one input, it can get tedious to write event handlers for each one of them. It's easier to use the
 `name` attribute to know which property in state should be updated. We also need to check whether the input is a
 checkbox, so we update the right prop:

```jsx
class Form extends React.Component {
    
    state = {
        name: '',
        email: '',
        consent: false,
    }
    
    handleChange = event => {
        const target = event.target;
        const value = target.type === 'checkbox' ? target.checked : target.value;
        this.setState({[target.name]: value});
    }
    
    render() {
        return (
            <form>
                <label htmlFor="name">Name:</label>
                <input
                    type="text"
                    id="name"
                    name="name"
                    value={this.state.input}
                    onChange={this.handleChange}
                />

                <label htmlFor="email">Email:</label>
                <input
                    type="email"
                    id="email"
                    name="email"
                    value={this.state.input}
                    onChange={this.handleChange}
                />
        
                <label htmlFor="consent">Can we email you spam?</label>
                <input
                    type="checkbox"
                    id="consent"
                    name="consent"
                    checked={this.state.consent}
                    onChange={this.handleChange}
                />
        
                <button type="submit">Submit</button>
            </form>
        );
    }
    
}
```

### Form submission

Forms take an `onSubmit` event handler. It's much easier to get our input data now that it's all in state — we just have
 to grab it and do what we want with it. Don't forget to reset everything after you submit!

```jsx
class Form extends React.Component {
  
    state = {
        name: '',
        email: '',
        consent: false,
    }
    
    handleChange = event => {...}
    
    handleSubmit = event => {
        event.preventDefault();
        const data = JSON.stringify(this.state);
        fetch('https://myserver.com/submit-form', {
            method: 'post',
            body: data,
        });
        this.setState({
            name: '',
            email: '',
            consent: false
        });
    }
    
    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                ...
            </form>
        );
    }
    
}
```

#### Final note:

You may have noticed we've been using `htmlFor` on our labels, instead of `for`. This is another JSX difference you just
 have to remember — `for` is a reserved word in JS so we can't use it here.



## List Items:

#### Rendering Multiple Components

You can build collections of elements and include them in JSX using curly braces {}.

Below, we loop through the numbers array using the JavaScript _map()_ function. We return an `<li>` element for each 
item. Finally, we assign the resulting array of elements to `listItems`:

```jsx
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
    <li>{number}</li>
);
```
We can include the entire listItems array inside a `<ul>` element, and render it to the DOM:

```jsx
ReactDOM.render(<ul>{listItems}</ul>, root);
```

#### Basic List Component

Usually you would render lists inside a component.

We can refactor the previous example into a component that accepts an array of numbers and outputs an unordered list of
 elements.

```jsx
function NumberList(props) {
    
    const listItems = props.numbers.map((number) =>
        <li>{number}</li>
    );
    
    return (
        <ul>{listItems}</ul>
    );
    
}

const numbers = [1, 2, 3, 4, 5];

ReactDOM.render(<NumberList numbers={numbers} />, root);
```

When you run this code, you’ll be given a warning that a `key` should be provided for list items. A `key` is a special
 attribute you need to include when creating lists of elements. We’ll discuss why it’s important in the next 
 section.  

Let’s assign a key to our list items inside props.numbers.map() and fix the missing key issue:

```jsx
function NumberList(props) {
  
    const listItems = props.numbers.map((number) =>
        <li key={number}>
            {number}
        </li>
    );
    
    return (
        <ul>{listItems}</ul>
    );
    
}

const numbers = [1, 2, 3, 4, 5];

ReactDOM.render(<NumberList numbers={numbers} />, root);
```


#### Keys

Keys help React identify which items have changed, are added, or are removed. Keys should be given to the elements
 inside the array to give the elements a stable identity. The best way to pick a key is to use a string that uniquely
 identifies a list item among its siblings. Most often you would use IDs from your data as keys:

```jsx
const todoItems = todos.map((todo) =>
    <li key={todo.id}>
        {todo.text}
    </li>
);
```

When you don’t have stable IDs for rendered items, you may use the item index as a key as a __last resort__:

```jsx
const todoItems = todos.map((todo, index) =>
    // Only do this if items have no stable IDs
    <li key={index}>
        {todo.text}
    </li>
);
```

We don’t recommend using indexes for keys if the order of items may change. This can negatively impact 
performance and may cause issues with component state. If you choose not to assign an explicit key to 
list items then React will default to using indexes as keys.

#### Extracting Components with Keys

Keys only make sense in the context of the surrounding array.

For example, if you extract a ListItem component, you should keep the key on the `<ListItem />` elements in the array
 rather than on the `<li>` element in the _ListItem_ itself.

```jsx
function ListItem(props) {
    return <li>{props.value}</li>;
}

function NumberList(props) {
    
    const numbers = props.numbers;
    
    const listItems = numbers.map((number) =>
        <ListItem
            key={number}
            value={number}
        />
    );
    
    return <ul>{listItems}</ul>;
    
}

const numbers = [1, 2, 3, 4, 5];

ReactDOM.render(<NumberList numbers={numbers} />, root);
```

A good rule of thumb is that elements inside the map() call need keys.

#### Embedding map() in JSX

JSX allows embedding any expressions in curly braces so we could inline the map() result:

```jsx
function NumberList(props) {
  
    const numbers = props.numbers;
  
    return (
        <ul>
            {numbers.map((number) =>
                <ListItem
                    key={number}
                    value={number}
                />
            )}
        </ul>
    );
    
}
```

## Exercise

1. Open `index.html` in your editor and browser.
2. Complete the base code to have a form with 3 input fields (text for the name, radiobutton for the sex and dropdown 
(select) for the age group).
3. Make all your input fields 'controlled components'.
4. When submitting, create a new object with the submitted data and save it into an array.
5. Render the array as a table under the form.

Next: [Stopwatch exercise](../05-exercise-stopwatch)

## Further Reading

- [Forms | React Docs](https://reactjs.org/docs/forms.html)
- [Lists and Keys | React Docs](https://reactjs.org/docs/lists-and-keys.html)
- [Why indexes are not the best keys for list items](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)

## License

Copyright © Progmasters (QTC Kft.), 2018.
All rights reserved. No part or the whole of this Teaching Material (TM) may be reproduced, copied, distributed, publicly performed, disseminated to the public, adapted or transmitted in any form or by any means, including photocopying, recording, or other electronic or mechanical methods, without the prior written permission of QTC Kft. This TM may only be used for the purposes of teaching exclusively by QTC Kft. and studying exclusively by QTC Kft.’s students and for no other purposes by any parties other than QTC Kft.
This TM shall be kept confidential and shall not be made public or made available or disclosed to any unauthorized person.
Any dispute or claim arising out of the breach of these provisions shall be governed by and construed in accordance with the laws of Hungary.
