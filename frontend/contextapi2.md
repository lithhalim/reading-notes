# Despite React’s popularity, one of the biggest obstacles developers face when working with the library is components re-rendering excessively, slowing down performance and harming readability. Component re-rendering is especially damaging when developers need components to communicate with each other in a process known as prop drilling.

The new React Context API, introduced with React v.16.3, allows us to pass data through our component trees, giving our components the ability to communicate and share data at different levels. In this tutorial, we’ll explore how we can use React Context to avoid prop drilling. First, we’ll cover what prop drilling is and why we should avoid it.

# Table of contents:

Components and props in React
React prop drilling
Getting started with React Context
React Context API examples
What the React Context API is used for
Redux vs. React Context API
Components and props in React
While your application might start out with just a single component, as it grows in complexity, you must continually break it up into smaller components. With components, we can isolate individual parts of a larger application, providing a separation of concern. If anything in your application breaks, you can easily identify where things went wrong using fault isolation.

# However, components are also meant to be reusable. You want to avoid duplicate logic and prevent over-abstraction. Reusing components comes with the benefits of DRY code; components usually have some data or functionality that another component needs, for example, to keep components in synchronization. In React, we can use props to make our components communicate.

Components are like JavaScript functions that can accept any number of arguments. Ideally, a function’s arguments are used for its operation. I like to think of a function as a block of code that performs a function with either zero or any number of arguments passed to it. For example, take the following function sum that adds two numbers, a and b:
```javascript
function sum(a, b) {
  return a + b;
}
Executing the function is fairly straightforward:

console.log(sum(1, 2)); // 3
In React components, these arguments are called props, short for properties. An ErrorMessage can look something like:

function ErrorMessage(props) {
  return (
    <div className="error-message">
      <h1> Something went wrong </h1>  
      <p> {props.message} </p>
    </div>
  )
}
```


# Because ErrorMessage will be reused many times across the app, it will pass a different message in its props. However, this is just one component, and this example doesn’t clarify where the message prop came from, which is important for us to know.

React prop drilling
React keeps UI changes in the virtual DOM, then updates the browser DOM through a process known as reconciliation. Let’s take a simple dashboard app as an example:


Over 200k developers use LogRocket to create better digital experiences
Learn more →

```javascript
function App() {
  const [title, setTitle] = React.useState("Home");
  const [username, setUsername] = React.useState("John Doe");
  const [activeProfileId, setActiveProfileId] = React.useState("A1B2C3");

  return (
    <div className="app">
      <h1>Welcome, {username}</h1>
      <Dashboard {...{ activeProfileId, title, username}}/>
    </div>
  )
}
```
The App component has three states, activeProfileId, title, and username. The states have default values, and they are passed down to the Dashboard component:

```javascript
function Dashboard({activeProfileId, title, username}) {
  return (
    <div className="dashboard">
      <SideNav {...{activeProfileId}}/>
      <Main {...{title, username}}/>
    </div>
  )
}
```
The Dashboard component receives the props and immediately dispatches them to subsequent components SideNav and Main further down the tree:

```javascript
function SideNav({activeProfileId}) {
  return (
    <nav className="side-nav">
      <h1>ID: {activeProfileId}</h1>
    </nav>
  )
}
```

function Main({title, username}) {
  return (
    <div className="main-content">
      <TopNav {...{title}}/>
      <Page {...{username}}/>
    </div>
  )
}
SideNav immediately consumes the activeProfileId prop, and Main continues to relay the title and username props further down the tree.
function TopNav({title}) {
  return (
    <nav className="top-nav">
      <h1> {title} </h1>
    </nav>
  )
}

function Page({username}) {
  return <Profile {...{username}}/>
}
TopNav uses the title props, and Page sends username down, again, to Profile:
function Profile({username}) {
  return <h1>{username}</h1>
}
Finally, Profile uses the username props. Passing props down in this manner, known as prop drilling, is the default method. To better illustrate the component hierarchy, view the diagram below:

React Prop Drilling Dashboard Example
App is the initiating prop-passing component. While App‘s states title, username, and activeProfileId were passed down as props, the components that needed those props were SideNav, TopNav, and Profile. However, we had to go through intermediary components Dashboard, Main, and Page, which merely relayed the props.

Traversing from App to Dashboard to SideNav is relatively easy compared to navigating from App, Dashboard, Main, Page, and finally to Profile.

Along the chain, anything could go wrong. For example, there could be a typo, refactoring could occur in the intermediary components, or our props might experience a mutation. Also, if we remove a single intermediary component, the whole process will fall apart.

More great articles from LogRocket:
Don't miss a moment with The Replay, a curated newsletter from LogRocket
Use React's useEffect to optimize your application's performance
Switch between multiple versions of Node
Learn how to animate your React app with AnimXYZ
Explore Tauri, a new framework for building binaries
Compare NestJS vs. Express.js
Discover popular ORMs used in the TypeScript landscape
