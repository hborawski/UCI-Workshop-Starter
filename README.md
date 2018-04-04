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
- Integrate REST services into application




# Workshop

## Explanation

### index.html
This project runs a local development server, and serves the `index.html` file on the default route (`http://localhost:8080`)

This html file is barebones and contains a `script` tag to include our bundled react app, and a `div` tag with an id that we can reference.

### index.js

This is the main entrypoint for the react application. Here, we import the `render` method from `react-dom`. This will let us render our app into the webpage. It does so by getting a reference to an html element:

```javascript
document.getElementById('main')
```

The first argument of the `render` function is the content we want to render. For now, we are simply rendering "Hello World"

```jsx
<div>Hello World</div>
```

## Creating the first component

Create a new file called ```Main.jsx``` under the ```src``` directory. 

JSX is a syntax extension of the JavaScript language. It isn't required to work with JSX, but it makes it much easier to work with UI elements within JS code. In this workshop we will create a component with plain JS and one with JSX to explore the benefits of using JSX.



### Plain Javascript

In your `Main.jsx` file, import react and set up a new class:

```javascript
import React from 'react';

export default class Main extends React.Component {
    
}
```

Although our file has a `jsx` extension, we can still write plain JS in here.

`export default` means that when we import this file from another file, we get access to this class.

#### Rendering

All React components must have a `render` function. This function's main purpose is to render a template view with data.

Let's add a `render` function to our `Main.jsx` component:

```javascript
export default class Main extends React.Component {
  render() {
    return React.createElement()
  }   
}
```

Since we are using plain javascript, we will be creating react elements using `createElement()` from the React library API. 

`createElement` takes 3 arguments: `type`, `props` and `children`.

Let's add the following to `render()`

```javascript
render() {
  return React.createElement('div', null, 'Hello From a new Component!')
}
```

We are giving this element a type `div` and a ```string``` as a child. This will eventually render into an html element. We will talk about props later.

Now in `index.js` we need to import our new component and render it instead of the old `<div>Hello World</div>` we had in there:

```javascript
import React from 'react'
import {render} from 'react-dom'
import Main from './Main'

render(React.createElement(Main, null ,null), document.getElementById('main'))

```

If you switch back to your browser at `http://localhost:8080`, you should see `Hello From a new Component!` on the page; great!

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

What just happened?

At this level there doesn't seem to be a dramatic difference, but lets think about HTML and how it is structured. HTML elements are trees, and generally nested pretty heavily. If you were to continue with our original `createElement` approach, you would end up with something like this:

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

Whereas in JSX you would have:

```jsx
return <div>
	<ul>
	  <li>List Item 1</li>
      <li>List Item 2</li>
	</ul>
</div>
```

JSX allows us to structure code in a more uniform manner. It makes it easier to dissect the parts of a component and reduces the amount of boilerplate code needed to render.

### Props

Props are a way to make components dynamic. Parent elements can pass any number of variables or primitives to child components. Let's take a look at making our `Main.jsx` say something different:

```jsx
render() {
    return <div>Hello {this.props.message}</div>
}
```

`props` is a member of our class, so we access it through `this` to reference our current instance. Beyond that, `props` is just a regular javascript object, mapping keys to values. In this scenario, we are looking to use the value of the `message` key from our props. 

Let's pass in `message` from `index.js`:

```jsx
render(<Main message='from props'/>, document.getElementById('main'))
```

Now when you visit your webpage, you should see the message `Hello from props`

## Building our Dog Quiz site

Now that you know how to make React components, we can use `Main.jsx` as a container to hold various components and interact with the API.

### DogImage.jsx

We want to create a component that displays the image of a dog. While we could just use an `img` tag, we would like to show some loading text during transitions between dogs without putting too much conditional logic in `Main.jsx`. 

Let's start by creating a file `DogImage.jsx` under `src` and getting an image to load:

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

Now when you load your page, you should see the image of the dog 🐶!  

But since you know what breed that dog is, it's not much of a game, so let's get a new image URL each time the page loads.

### Data fetching and State

We make network requests in javascript with the `fetch` method. This is a part of the browser javascript engine and is readily available in the global namespace:

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

The `fetch` method returns a `Promise` which is javascripts way of handling asynchronous tasks. We won't be going in depth on promises here, but we will provide all of the promise based code. All you need to know right now is that when a `fetch` call is complete, the `Promise` returned contains access to data that we requested. In our case, we make a call via `fetch('https://dog.ceo/api/breeds/image/random')` which returns something like this:

```
{"status":"success","message":"https:\/\/dog.ceo\/api\/img\/springer-english\/n02102040_1217.jpg"}
```

But what do we do with this? How do we update our application to show these new dog images when we generate them? This is where `state` comes in. By setting the URL of the returned image as part of the `state` object, when the state changes, so does our presentation of the state. React makes this very simple.

#### Setting up state

We need to add an initial state to our application, we do this in the constructor:

```jsx
// Main.jsx

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

Now we need to update the state when we get the new url. If you take a look at the sample response from above, you'll see the url is in the `message` part of the response, so we extract it by converting the response into a JSON object and setting `url: json.message` in `this.setState()`:

```jsx
// Main.jsx

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

Now when `getDogImage` is called, the `url` in `state` will update with the new returned response. But how will that update the interface?

#### Reacting to state changes

Any usage of `this.state` in the `render` method of a component will trigger it to re-render when there is a change to the member in use:

```jsx
// Main.jsx

render() {
  return <div>
    <DogImage url={this.state.url}/>
  </div>
}
```

Now we just need to make sure `getDogImage` is called when the application loads to get our first image.

### React Lifecycle

We will use `componentWillMount` to fetch an image when the application loads. This is part of the React Lifecycle, but we will not go in depth on this subject here. To do this we need to make two changes:

The first change is in the constructor. We need to bind the context of `getDogImage` to the instance of our component:

```jsx
// Main.jsx

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
// Main.jsx

componentWillMount() {
	this.getDogImage()
}
```

Now whenever you reload your page, you will see a different dog image!

#### Main.jsx

The full `Main.jsx` will now look something like this:

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

