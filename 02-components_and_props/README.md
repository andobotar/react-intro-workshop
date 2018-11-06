# Component Proponent

We've seen how to create React elements and pass them around as variables. They aren't very useful though, 
as we can't compose our JSX in the same way we would HTML. There is also no way to update an element — you'd have to 
create a new version and re-render that to the DOM. Whilst React is smart enough to only update the bits of the DOM 
that have actually changed ([more on this here](https://reactjs.org/docs/rendering-elements.html)), this isn't ideal for
 building a UI.

At this point we need to distinguish between _elements_ and _components_. As we've seen an element is an object 
describing what you want in the DOM. A _component_ is more like a function: they accept inputs (called `props`) and
 return React elements.

The simplest way to define a component is with a function:

```jsx
function Title(props) {
  return <h1>Hello world!</h1>;
}
```

We could also use arrow functions to achieve this, however, that would create an anonymous function, which would make debugging more difficult (this is the only place where we are going to use the __function__ keyword instead of an arrow function):

#### Note:

React components should act like pure functions with respect to their props. 
Don't try to change the props from within the component. Props should only get updated by a component 
higher up the tree passing new props in (which is equivalent to calling the component function again with new arguments).

### Rendering components

So how do we render them? Elements can represent either HTML elements or user-defined components. 
Unlike our naive `createElement` function from part 1, `React.createElement` can take more than an HTML tag name as the
 first argument. 
It can also take a React component as a variable. For example, if we have created the `Title` component from above:

```jsx
ReactDOM.render(<Title />, root);
// renders <h1>Hello world!</h1>
```


All components must return a __single__ element. If our component consists of several parts, 
we need an enclosing tag (e.g. a `<div>`) that encapsulates them:

```jsx
function Article(props) {
    return (
        <div>
            <h1>I am the title</h1>
            <p>I am the body</p>
        </div>
    )
}
```

#### Note:

- An enclosing `<div>` element will render an often unnecessary, empty `<div>` on the screen. If we want to avoid this, we can use React's built-in component, which is designed exactly for this purpose. It's called `<Fragment>`:  

    ```jsx
    <Fragment>
        <h1>I am the title</h1>
        <p>I am the body</p>
    </Fragment>
    ```
    
- React requires tags with no children to self close (ending with `/>`)
- React treats lowercase tag names as HTML and capitalised tags as components, 
so be sure to always capitalise your component variables.

### Customising components with props

As discussed, the component function can receive a _props_ parameter, which customizes what the component is going to display. 
In JSX we simply add `<Title name="Jessica" />`. We can use anything passed in our component this way:

```jsx
function Title(props) {
    return <h1>Hello {props.name}</h1>;
}

ReactDOM.render(<Title name="Jessica" />, root);

// renders <h1>Hello Jessica</h1>
```

There's a new bit of JSX syntax here: we can embed
 [JavaScript expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions) 
 in our JSX using curly braces. As long as it resolves to a value you can put any JS you want in here. For example:

```jsx
function Title(props) {
    <h1>Hello {props.name + '!'}</h1>
} 

ReactDOM.render(<Title name="Ms. Alba" />, root);

// renders <h1>Hello Ms. Alba!</h1>
```

This means we can do things like ensure something will be rendered even if no prop is passed:

```jsx
function Title(props) {
    return <h1>Hello {props.name || 'world'}</h1>;
}
ReactDOM.render(<Title />, root);

// `props.name` is undefined, so renders <h1>Hello world</h1>
```

### Children

Children is a normal prop with a couple of extra privileges. It can be passed either like any other prop:

```jsx
function Title(props) {
    return  <h1>{props.children}</h1>;
}

ReactDOM.render(<Title children="Hello world" />, root);
```

or between two tags just like in HTML:

```jsx
ReactDOM.render(<Title>Hello world</Title>)
```

It's generally easier (and more similar to HTML) to pass children between the tags.

### Composition

We can pass React components to each other as children. This unlocks one of React's most powerful ideas: 
__composition__. It means we can encapsulate functionality in a component and then wrap that component around whatever we like.

Imagine we wanted a component that rendered some "card" features (border, box-shadow etc):

```jsx
function Card(props) {
    return (
        <div className="card">
            <h2>{props.name}</h2>
            <p>{props.description}</p>
        </div>
    );
}
```

We can now construct very readable interfaces by using our custom component:

```jsx
function Menu(props) {
    return (
        <div className="menu__container">
            <h1>Welcome to PROGmasters Restaurant!</h1>
            <Card 
                name={props.foods[0].name} 
                description={props.foods[0].description} 
            />
            <Card
                name={props.foods[1].name} 
                description={props.foods[1].description}
            />
        </div>
    );
}

const foodList = [
    {name: 'Hummus', description: 'Our hummus is so tasty'},
    {name: 'Ice-cream', description: 'Extremely icy and incredibly creamy'}
];

ReactDOM.render(<Menu foods={foodList} />, root);
```

The `<Menu>` component shouldn't have to care about the implementation details of a `<Card>`. 
This is a contrived example but you can have as much complexity as you like concealed behind a simple JSX tag, 
which gets very powerful.

<img src="react-intro-workshop/assets/progmasters_restaurant_menu.png" alt="menu_components" width="500" />
<br/>
Check out the code here: [PROGmasters_menu_code](composition/menu.html)  
  

### Splitting up components

Imagine we had a chunk of HTML like this representing a blog post excerpt:

```html
    <article class="post">
        <div class="post__heading">
            <h2 class="post__title">Everyone should eat hummus</h2>
            <span class="post__subtitle">Hummus is the best</span>
        </div>
        <div class="post__body">
            <p>This easy dip recipe is great to make sandwiches for your lunchbox, or simply to serve with breadsticks or pitta</p>
        </div>
        <footer class="post__footer">
            <span>Posted at <time>2018-04-20</time></span>
        </footer>
    </article>
```

We might have lots of these on a single page, which would get pretty unwieldy to read through and work with. We could 
insert this HTML straight into a React component, but it would be better if we could break this large component down
 into smaller parts:

```jsx
function Heading(props) {
    return (
        <div className="post__heading">
            <h2 className="post__title">{props.title}</h2>
            <span className="post__subtitle">{props.subtitle}</span>
        </div>
    );
}

function Body(props) {
    return (
        <div className="post__body">
            <p className="post__summary">{props.children}</p>
        </div>
    );
}

function Footer(props) {
    return (
         <footer className="post__footer">
              <span>Posted at <time>{props.time}</time></span>
         </footer>
    );
}
```

We can then compose our Post component together from these parts:

```jsx
function Post(props) {
    return (
        <article className="post">
            <Heading title={props.title} subtitle={props.subtitle} />
            <Body>{props.children}</Body>
            <Footer time={props.time} />
        </article>
    );
}

const hummusPost =
    <Post
        title="Everyone should eat hummus"
        subtitle="Hummus is the best"
        time="2018-04-20"
    >
    This easy dip recipe is great to make sandwiches for your lunchbox,  
     or simply to serve with breadsticks or pitta
    </Post>
    
ReactDOM.render(hummusPost, root);
```

Our Post component is easier to understand at a glance, with more meaningful tag names than the standard HTML, and we've
 made it easier to feed data into the component as well.

## Exercise 1

1. Work in __components_intro.html__. We have defined two components for you, `Container` and `Picture`.
2. Pass the URL from the `Container` to the `Picture` as a __prop__.
3. Extract that URL in the `Picture` component and use it as the _src_ of the `<img>` tag.
4. You should see a beautiful picture appear in your browser.
5. Make sure there are no error or warning messages in the console of your browser.

## Exercise 2

1. Open __index.html__ in your editor and browser. You should see the `<h1>` rendered to the page.
2. Create your own _EssayBody_ component with a `<span>` and a `<p>` element inside it. The `<span>` should hold the
 author's name and the `<p>` should hold the actual text of the essay. Create an Essay element, that consists of a Title
 component and your newly created EssayBody. Use _props_ to pass data between the components.
3. Render your new component to the page.

Next: [Class-based components](../03-classes_and_state)

## Further Reading

- [Components and Props | React Docs](https://reactjs.org/docs/components-and-props.html)
- [Composition vs Inheritance | React Docs](https://reactjs.org/docs/composition-vs-inheritance.html)

## License

Copyright © Progmasters (QTC Kft.), 2018.
All rights reserved. No part or the whole of this Teaching Material (TM) may be reproduced, copied, distributed, publicly performed, disseminated to the public, adapted or transmitted in any form or by any means, including photocopying, recording, or other electronic or mechanical methods, without the prior written permission of QTC Kft. This TM may only be used for the purposes of teaching exclusively by QTC Kft. and studying exclusively by QTC Kft.’s students and for no other purposes by any parties other than QTC Kft.
This TM shall be kept confidential and shall not be made public or made available or disclosed to any unauthorized person.
Any dispute or claim arising out of the breach of these provisions shall be governed by and construed in accordance with the laws of Hungary.
