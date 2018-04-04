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

### Stateful vs Stateless

State is useful when we need our UI to react to changes in the information that our component keeps track of, but not all components will keep track of state. If we take a look at our  `DogImage` component, it ends up being pretty simple. It uses the URL given to it to return an `img` tag. If it has an input and an output, and doesn't modify anything else, does it need to have all the same functionality as a component that maintains state? It does not, and react provides a way to do this. When it boils down to it, the `DogImage` component is nothing more than a function, it takes in props and returns a react element. So we can rewrite it as a function that is just the render portion from the class:

```jsx
const DogImage = (props) => {
    return <img src={props.url}/>
}

export default DogImage
```

**Note:** since we are just using a function now, and not a class, all references to `props` are just to `props`, and not through `this.props`.

While we are here, lets extend the functionality of the `DogImage` component to indicate when it's loading a new image. We set up our initial state to have the `url` be `null`. In our component we can check if `props.url` is null and conditionally return a different element:

```jsx
const DogImage = (props) => {
    if (props.url !== null) {
        return <img src={props.url}/>
    } else {
        return <p>Loading</p>
    }
}

export default DogImage
```

Now you will see your loading text while the API request is being made, and it will be switched for the image once the url is updated in the state.

### User Input

React was created to build SPA's, single page apps. So we shouldn't be reloading our page every time to get new content, we should have it fetch new data and update the state. So let's hook up a button to get a new dog image.

```jsx
//Main.jsx
...
render() {
  return <div>
	<DogImage url={this.state.url}/>
	<button onClick={this.getDogImage}>New Dog</button>
  </div>
}
```

Here we are passing a reference to the `getDogImage` function to the `onClick` attribute for the button. Now your page should have a button that loads a new dog image when you click it! But wait, where did the loading text go? Since we only show the loading text if `props.url` is `null`, that only happens when the page first loads. To make our loading text show every time the image is loading, let's reset the `url` in our state each time we fetch a new image:

```jsx
getDogImage() {
    this.setState({
      url: null
    })
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
```

By setting the state for `url` back to null before making our API call, it will show "Loading" until the network request completes and we have the new url. Add this to your `getDogImage` method and try it out.

## Guessing

Now that we can fetch new images without reloading the page, we can move on to the guessing portion of the app. To do this, we need to get the breed of the current displayed dog. The breed is a part of the path for the image, if we split on `/` it's the 6th element in the array, so let's add it to our state:

```jsx
constructor(props) {
    super(props)
    this.state = {
      url: null,
      breed: null // add the breed to the initial state as null
    }
    this.getDogImage = this.getDogImage.bind(this)
  }
```

And when we fetch the image, we set it in the state:

```jsx
getDogImage() {
    this.setState({
      breed: null, // reset the breed when getting a new image
      url: null
    })
    fetch('https://dog.ceo/api/breeds/image/random')
    .then((response) => {
      return response.json()
    })
    .then((json) => {
      this.setState({
        url: json.message,
        breed: json.message.split('/')[5] // pull element 6 (index 5) from the split url
      })
    })
  }
```

For debugging purposes, we can display the current breed in our `render` function, and remove it later once we have everything working:

```jsx
render() {
    return <div>
      <p>{this.state.breed}</p>
      <DogImage url={this.state.url}/>
      <button onClick={this.getDogImage}>New Dog</button>
    </div>
  }
```

Now you should see the breed of the current dog displayed.

### Making Choices

To create the choices for buttons for our users to click, we need to get random choices to pad around the real answer. We can use the `https://dog.ceo/api/breeds/list/all` endpoint to get all the breeds and subbreeds of dogs. The structure of this response is an object where the key is the breed, and the value is an array of subbreeds. The format we have for our current breed is `${breed}-${subbreed}` so we will need to do some processing on this data to get it in the format we need. Rather than go through this step by step, we will provide the code for this.

First, let's setup the initial state for the full list of breeds:

```jsx
constructor(props) {
    super(props)
    this.state = {
      url: null,
      breed: null,
      allBreeds: []
    }
    this.getDogImage = this.getDogImage.bind(this)
  }
```

Then we can add a `getChoices` function to get the list of breeds:

```jsx
getChoices() {
    fetch('https://dog.ceo/api/breeds/list/all')
    .then(res => res.json())
    .then(data => {
      // The response from the api is an object where the key is breed name
      // and the value is an array of sub-breeds
      const breedNames = Object.keys(data.message)
      // Map returns a new array that is populated with the return values from
      // the function passed to it
      const subbreedArrays = breedNames.map(breed => {
        const subbreeds = data.message[breed]
        // If the length is 0, there are no sub-breeds
        if (subbreeds.length === 0) {
          // return it as an array so we can concatenate it later to other arrays
          return [breed]
        } else {
          // If there are sub-breeds, we need to merge the main breed name
          // with the sub-breed name
          return subbreeds.map(subbreedName => `${breed}-${subbreedName}`)
        }
      })
      const flattenedBreedArray = subbreedArrays.reduce(
        // Merge the arrays by concatenating them
        (accumulation, currentValue) => accumulation.concat(currentValue)
      )
      this.setState({
        allBreeds: flattenedBreedArray
      })
    })
  }
```

And we can reference this the same way we did for `getDogImage`:

In `constructor`:

```jsx
this.getChoices = this.getChoices.bind(this)
```

And in `componentWillMount`:

```jsx
this.getChoices()
```

### MultipleChoice.jsx

Now we can create a component to handle creating the buttons that our user will click. This will take in the array of all breeds as a prop, as well as the current breed that `Main.jsx` has in it's state. This component will need to maintain it's own state so we can keep track of whether the guess was correct or not.

```jsx
import React from 'react'

export default class MultipleChoice extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            correct: null
        }
    }
    
    render() {
        return <div></div>
    }
}
```

To make the choices we need to inject our current breed into a selection of random breeds from our list of all breeds. We are providing a function to randomize the choices for you as a time saver:

```jsx
createChoices(allBreeds, currentBreed, length = 4) {
  const shuffleArray = arr => (
    // Map to array of [randomFloat, originalValue]
    // sort based on the floats
    // pull original values back into single array
    arr.map(a => [Math.random(), a]).sort((a, b) => a[0] - b[0]).map(a => a[1])
  )
  // Remove our current breed from the possible choices before shuffling
  // we will add it back in later to ensure its a choice
  // NOTE: filter returns a new array and does not modify the original
  const choicesWithoutCurrentBreed = allBreeds.filter(b => b !== currentBreed)
  // Randomize the order of the array
  const shuffledChoices = shuffleArray(choicesWithoutCurrentBreed)
  // Pull the first few items off the array so we have a random selection
  // NOTE: slice returns a new array and does not modify the original
  const fewerChoices = shuffledChoices.slice(0, length -1)
  // Add on our actual breed so the right answer is available
  const choicesWithCurrentBreed = fewerChoices.concat(currentBreed)
  // Randomize the order again so we can't predict where the real answer is
  const shuffledFinalChoices = shuffleArray(choicesWithCurrentBreed)
  return shuffledFinalChoices
}
```

We can then use this function in our `render` function to create some choices. But don't forget to bind the context for this function!

```jsx
constructor(props) {
    super(props)
    this.state = {
        correct: null
    }
    this.createChoices = this.createChoices.bind(this)
}
...
render() {
    const choices = this.createChoices(this.props.allBreeds, this.props.currentBreed)
    return <div></div>
}
```

Now we have `choices` as an array of strings, we can generate the buttons using `map`. `map` returns a new array, so we just need it to return an array of buttons:

```jsx
render() {
    const choices = this.createChoices(this.props.allBreeds, this.props.currentBreed)
    return <div>
    	{
            choices.map(choice => <button>{choice}</button>)
    	}
    </div>
}
```

Now whenever you get a new dog image, it will have buttons that show a few different choices (including the correct one)! The only thing left is to make it so we can have our users guess and get some feedback. Let's add a function to handle updating the state with our guess, that takes the guess as a parameter:

```jsx
constructor(props) {
    super(props)
    this.state = {
        correct: null
    }
    this.createChoices = this.createChoices.bind(this)
    this.guessDog = this.guessDog.bind(this) // bind the context!
}
guessDog(breed) {
    this.setState({
        // Set correct to be a boolean that is the comparison of the guess and our current
        // breed. The reason we
        correct: breed === this.props.currentBreed
    })
}
```

And in our render function we need to call this function when a button is clicked. `onClick` takes a function as its value, and in `Main.jsx` we passed a reference to a function:

```jsx
<button onClick={this.getDogImage}>New Dog</button>
```

But here we need to pass a value to the function, so we will give `onClick` an anonymous function:

```jsx
<button onClick={() => this.guessDog(choice)}>
```

This way when the button is clicked, it runs this anonymous function, and the function it runs has the parameter to pass to `guessDog` already inserted. So now your render function will look like:

```jsx
render() {
    const choices = this.createChoices(this.props.allBreeds, this.props.currentBreed)
    return <div>
      {
        choices.map(choice => <button onClick={() => this.guessDog(choice)}>{choice}</button>)
      }
    </div>
  }
```

### Lifecycle Continued

If you go to your browser you will notice some interesting behavior, when you click one of the choices, the list will reshuffle. This is because we are updating the state, and when there are state updates, the `render` funciton gets called again, and that runs our `createChoices` code every time. 

We don't need to create choices each time we render, we only need to create the choices when the `currentBreed` prop changes. We will need to make a few changes for this to work.

1. Add `choices` to our state
2. Update the choices in our state when we get a new `currentBreed`
3. Use the choices from our state in the `render` method

So let's update our constructor for the initial state:

```jsx
constructor(props) {
    super(props)
    this.state = {
      choices: [],
      correct: null
    }
    this.guessDog = this.guessDog.bind(this)
    this.createChoices = this.createChoices.bind(this)
  }
```

Now we will implement another lifecycle method `componentWillReceiveProps` to update our state when we are getting a new breed. Since our component is always loaded, `componentWillMount` only gets triggered once, even if the props change. `componentWillReceiveProps` gets triggered every time the props are updated. When we fetch a new dog image, we first reset the `url` and `breed` to `null` before setting it to the final value. This is helpful for us now, we can check to make sure the `currentBreed` prop is not null in the update, and if it isn't update our choices. If we update when it is null, we will see the buttons render with a null button, then render again when the currentBreed gets set.

```jsx
// nextProps is the props the component is going to have
// we can use that to synchronize our state
componentWillReceiveProps(nextProps) {
    if(nextProps.currentBreed !== null) {
        this.setState({
            choices: this.createChoices(this.props.allBreeds, nextProps.currentBreed)
        })
    }
}
```

And finally, in our `render` method:

```jsx
render() {
    return <div>
      {
        this.state.choices.map(choice => <button onClick={() =>this.guessDog(choice)}>{choice}</button>)
      }
    </div>
  }
```

Now when you visit your browser, the buttons will only change when the dog image changes!

#### User feedback

Now let's add something so our user knows if they got the guess right or not, we already have this information in `this.state.correct` so it's just a matter of displaying it:

render() {

```jsx
return <div>
  {
    this.state.choices.map(choice => <button onClick={() =>this.guessDog(choice)}>{choice}</button>)
  }
  <p>{this.state.correct ? 'Correct!' : 'Wrong!'}</p>
</div>
```
 Now when you load up, it'll start by saying "Wrong!" but when you choose the correct answer, it'll switch to "Correct!".

#### Ternary Operator

When you are working inside JSX tags, you aren't able to use traditional `if/else` conditionals. This is because all javascript statements (inside curly braces) must return a react element. There are a couple ways to handle this, the most common way is using the ternary operator as we just did, or you can do the computation before you start your tags, and interpolate the variable after, such as:

```jsx
render() {
	let message = '';
    if (this.state.correct) {
        message = 'Correct!'
    } else {
        message = 'Wrong!'
    }
    return <p>{message}</p>
}
```

However, that code is more verbose, and react is primarily a *functional* library, so generally, using a solution that does not use mutable variables is preferred.

##### Ternary Syntax

The ternary operator is a single conditional that returns a value:

```jsx
comparisonCondition ? returnValueIfTrue : returnValueIfFalse
```

### Cleaning up

There are still a few remaining quirks we need to clean up before we can call it a day, the first one being that by default, we tell a user they are wrong before they have even guessed. This is why we initially set our state of `correct` to null. Once a user guesses, `correct` will be a boolean, so we can use a null check to see if the user has guessed or not:

```jsx
<p>{this.state.correct === null ? '' : this.state.correct ? 'Correct!' : 'Wrong!'}</p>
```

Here we are nesting our ternary operators, we check if `correct` is null, if it is, we return empty string, otherwise, we return the result of our original ternary condition. Now when you load it up, you won't see feedback until you make a guess. But once you do, it'll persist between dogs! Not a problem, we know when we're getting a new dog, so we can reset our state for `correct` then.

```jsx
componentWillReceiveProps(nextProps) {
    if (nextProps.currentBreed !== null) {
      this.setState({
        correct: null,
        choices: this.createChoices(this.props.allBreeds, nextProps.currentBreed)
      })
    }
  }
```

Now when the dog reloads, we get a clean slate to start guessing. The last thing to do is to remove our debug with the current breed so we actually need to guess! So head back to `Main.jsx` render method and remove:

```jsx
<p>{this.state.breed}</p>
```

