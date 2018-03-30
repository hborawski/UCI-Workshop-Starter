# React Workshop

## Setup

- Install the Node.js runtime one of the following ways:
  - Install from Node.js website: https://nodejs.org/en/download/
  - Available in homebrew (https://brew.sh/)
  - DIY 🤔
- Clone/download this repository
- In your terminal navigate to the directory you just downloaded and run `npm install`

### Running the project

- `npm start`
- In your browser navigate to http://localhost:8080
- This project uses `webpack-serve` to bundle, run, and watch for changes, so once you have it running, it will keep compiling changes and refreshing the browser for you!

### Goals

This workshop will teach you the following:
- Create and render React components
- Work with props and states
- Difference between Stateful and Stateless
- Integrate with the Redux Store
- Integrate REST services into application




# Workshop

## Explanation

### index.html
This project runs a local development server, and serves the `index.html` file on the default route (`http://localhost:8080`)

This html file is barebones and contains a `script` tag to include our bundled react app, and a `div` tag with an id that we can reference.

### index.js

This is the main entrypoint for the react application, here we import the `render` method from `react-dom`, this will let us render our app into the webpage. It does so by getting a reference to an html element:

```javascript
document.getElementById('main')
```

The first argument for render is the content we want it to render. To start, we are simply rendering "Hello World"

```jsx
<div>Hello World</div>
```

## Creating the first component

Create a new file called ```Main.jsx``` under the ```src``` directory. 

JSX is a syntax extension of the JavaScript language. It isn't required to work with JSX, but it makes it much easier to work with UI elements within JS code. In this workshop we will create a component with plain JS and one with JSX to see the difference.



### Plain Javascript

In your `Main.jsx` file (don't worry about the `jsx` extension, we can write plain javascript in here), import react, and set up a new class:

```javascript
import React from 'react';

export default class Main extends React.Component {
    
}
```

`export default` means that when we import this file from another file, we get access to this class.

#### Rendering

All React components must have a `render` function. This function's main purpose is to populate the template view we will be showing users with updated data.

Let's add one to our component:

```javascript
export default class Main extends React.Component {
  render() {
    return React.createElement()
  }   
}
```

Since we are using plain javascript, we will be creating react elements using the React library API. `createElement` takes 3 arguments, `type`, `props` and `children`.

```javascript
render() {
  return React.createElement('div', null, 'Hello From a new Component!')
}
```

We are giving this element a type `div` and a ```string``` as a child. This will eventually render into an html element. This component doesn't need `props` yet, we will talk about props later.

Now in `index.js` we need to import our new component and render it instead of the old `<div>Hello World</div>` we had in there:

```javascript
import React from 'react'
import {render} from 'react-dom'
import Main from './Main'

render(React.createElement(Main, null ,null), document.getElementById('main'))

```

If you go look at the changes in your browser, you should see `Hello From a new Component!` on the page, great!

### JSX

Now let's look at how this is different when done with JSX. We can modify our `Main.jsx` file to use JSX very easily:

```jsx
export default class Main extends React.Component {
  render() {
    return <div>Hello From a new JSX Component!</div>
  }   
}
```

And in `index.js`:

```jsx
import React from 'react'
import {render} from 'react-dom'
import Main from './Main'

render(<Main/>, document.getElementById('main'))
```

#### Comparison

This might not look that much different at this level, but lets think about HTML and how it is structured. HTML elements are a tree, and are generally nested pretty heavily. If you were to to this with `createElement`, you would end up with something like this:

```javascript
return React.createElement(
  'div',
   null,
   React.CreateElement(
     'ul',
     null,
     React.createElement('li', null, 'List Item 1'),
     React.createElement('li', null, 'List Item 2'),
   )
 )
```

Where in JSX you would have:

```jsx
return <div>
	<ul>
	  <li>List Item 1</li>
      <li>List Item 2</li>
	</ul>
</div>
```

The JSX is generally easier to view the structure of a component, as well as reduce the amount of boilerplate code needed to render.

### Props

Props are a way to make your components dynamic, parent elements can pass any number of variables, or primitives to child components. Let's take a look at making our `Main.jsx` say something different:

```jsx
render() {
    return <div>Hello {this.props.message}</div>
}
```

`props` is a member of our class, so we access it through `this` to reference our current instance. Beyond that, `props` is just a regular javascript object, mapping keys to values. In this scenario, we are looking to use the value of the `message` key from our props. In `index.js` we can pass it in easily:

```jsx
render(<Main message='from props'/>, document.getElementById('main'))
```

Now when you visit your webpage, you should see the message `Hello from props`

## Building our Dog guessing site

Now that you know how to make React components, we can use `Main.jsx` as a container to hold various components and interact with the API.

### DogImage.jsx

We want to have a component to display the image of the dog, while we could just use an `img` tag, we would like to show some loading text while transitioning between dogs without putting too much conditional logic in `Main.jsx`. Let's start by getting an image to load:

```jsx
// DogImage.jsx
import React from 'react'
export default class DogImage extends React.Component {
  render() {
    return <img src={this.props.url} />
  }
}
```

And we can import this into `Main.jsx`:

```jsx
import React from 'react'
import DogImage from './DogImage'

export default class Main extends React.Component {
    render() {
        return <div>
        	<DogImage url='https://dog.ceo/api/img/terrier-patterdale/dog-1268559_640.jpg'/>
        </div>
    }
}
```

If you visit the API endpoint we will be using later (https://dog.ceo/api/breeds/image/random), you can get a random dog image URL to put in as a test.

Now when you load your page, you should see the image of the dog! But once you know what breed that one dog is, it wouldn't be much of a game, so let's get a new image URL each time the page loads.

### Data fetching and State

We make network requests in javascript with the `fetch` method. This is a part of the browser javascript engine, and is available in the global namespace:

```jsx
//Main.jsx

export default class Main extends React.Component {
    getDogImage() {
        fetch('https://dog.ceo/api/breeds/image/random')
        .then((response) => {
        	// Convert the response to JSON so its easier to work with
            return response.json()
        })
        .then((json) => {
			// Now we have a JSON object!
        })
    }
    render(){...}
}
```

The `fetch` method returns a `Promise` which is javascripts way of handling asynchronous tasks. We won't be going in depth on promises here today, but we will provide all of the promise based code.

But what do we do with the data? How do we get it to make changes in our application? We need to employ state into our application, where the URL of the current image is part of that state, and when the state changes, so does our presentation of the state. React makes this very simple.

#### Setting up state

We need to add an initial state to our application, we do this in the constructor:

```jsx
export default class Main extends React.Component {
  constructor(props) {
    super(props) // make sure we satisfy the constructor of the inherited class
    this.state = { // state needs to be an object
      url: null // we will set the url to null to start
    }
  }
}
```

#### Updating state

Now we need to update the state when we get the new url, if you visit the api endpoint manually, you'll see the url is in the `message` part of the response:

```jsx
getDogImage() {
        fetch('https://dog.ceo/api/breeds/image/random')
        .then((response) => {
        	// Convert the response to JSON so its easier to work with
            return response.json()
        })
        .then((json) => {
            // this creates a new state and sets it as the state of our component
			this.setState({
                url: json.message
			})
        })
    }
```

Now when `getDogImage` is called, it will update the `url` part of the state once it receives its API response. But how will that update the interface?

#### Reacting to state changes

Any usage of `this.state` in the `render` method of a component will trigger it to re-render when there is a change to the member in use:

```jsx
render() {
  return <div>
    <DogImage url={this.state.url}/>
  </div>
}
```

Now we just need to make sure `getDogImage` is called when the application loads to get our first image.

### React Lifecycle

We won't be going in depth for the lifecycle of React today, but we will use `componentWillMount` to fetch an image when the application loads. To do this we need to make two changes:



The first change is in the constructor, we need to bind the context of `getDogImage` to the instance of our component:

```jsx
constructor(props) {
    super(props)
    this.state = {
        url: null
    }
    this.getDogImage = this.getDogImage.bind(this)
}
```

This makes sure that when this instance of `Main` calls `getDogImage` it always is referencing the same instance when using `this.setState`. Since we will only have one instance of Main, there wouldn't be any problem, but this is a required step for React.



The second change is adding `componentWillMount`:

```jsx
export default class Main extends React.Component {
	componentWillMount() {
        this.getDogImage()
	}
}
```

Now whenever you reload your page, you will see a different dog image!

#### Main.jsx

The full `Main.jsx` now will look something like this:

```jsx
import React from 'react';
import DogImage from './DogImage'

export default class Main extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      url: null
    }
    this.getDogImage = this.getDogImage.bind(this)
  }

  componentWillMount() {
    this.getDogImage()
  }

  getDogImage() {
    fetch('https://dog.ceo/api/breeds/image/random')
    .then((response) => {
      return response.json()
    })
    .then((json) => {
      this.setState({
        url: json.message
      })
    })
  }
  render() {
    return <div>
      <DogImage url={this.state.url}/>
    </div>
  }
}

```

