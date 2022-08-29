# Sometimes we might set up the state or logic handling properly, but fail to consume the states. Or we might get everything working, but clutter up the codebase in the process, making it hard to read, adapt, and extend.  

The ability to have a single source of truth (a single store) is what gives Redux an upper hand over the traditional Context API.

# So, without any further ado, let's see how we can make the best use of Redux for application-wide state management by writing cleaner and more optimized code.

Before We Start
This article mainly looks at different ways to improve an existing Redux store. So you'll need to know the basics of Redux to get through it, such as setting up reducers, the Redux store, and dispatching actions.

If you need to brush up on Redux, check out these helpful guides:

Redux for Beginners
The Brain-friendly Guide to Learning Redux
Already know the basics of Redux and have a Redux store you want to improve? Good.

# Here's what we'll cover in this article:

A quick recap on how to use the Redux store
How to use the Redux Toolkit library by creating a Redux store for an e-commerce application. Note: we won't build the entire application, but just the state management part of it
How to handle asynchronous code outside of reduces and components by using action creators
And with that, let's get started.

# A Quick Redux Store Recap
Before looking at how we can improve our Redux code, let's have a brief look at how we've been using Redux up to this point.

Here's a basic example of a Redux store. It's for a simple counter application that can increment and decrement the count on button clicks, and the state of the counter is managed and handled by Redux:
```javascript
import { createStore } from 'redux'

const initialState = {
  counter: 0,
}

const reducer = (state = initialState, action) => { // Reducer function
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, counter: state.counter + 1 }
    case 'DECREMENT':
      return { ...state, counter: state.counter - 1 }
    default:
      return state
  }
}

const store = createStore(reducer) // redux-store

export default store
redux-store.js
import { useDispatch, useSelector } from 'react-redux'

export default function Component(props) {
  const dispatch = useDispatch()
  const counter = useSelector((state) => state.counter)

  const incrementHandler = () => {
    dispatch({ type: 'INCREMENT' })
  }
  const decrementHandler = () => {
    dispatch({ type: 'DECREMENT' })
  }

  return (
    <div>
      <h1>Counter:{counter}</h1>
      <button onClick={incrementHandler}>Increment</button>
      <button onClick={decrementHandler}>Decrement</button>
    </div>
  )
}

```
Component.jsx
This is a very simple example that you could create just using state hooks. But it serves as a good overview of how we'd typically use Redux.

The Redux store above is perfectly fine if there aren't many different types of logic that need to be handled or dispatched.

Also, note that I used const initialState instead of var or let even though we are updating the state because of the property of immutability. That is, we aren't really mutating the existing state, but instead are reconstructing a new state altogether with the updated values.

This is fine. But what if the complexity of the application grows and requires us to perform multiple state and logic handling?

That's exactly what we'll cover in this guide.

How to Create a Redux Store With Redux Toolkit
Throughout the rest of the article, let's consider how to manage state across an e-commerce application. In an e-commerce app, we would want to track and broadcast multiple states across various components.

As a rule of thumb, it is always good to have a rough idea of how we'll implement a design prior to getting our hands dirty.

When we think of an e-commerce application, the possible significant states are likely the following:

Authentication
Cart tracking
Watchlist
We can divide the state handling logic and the state into different modules, and then combine it all into a single store.

For this, we'll use Redux Toolkit.

Redux Toolkit is a third-party library like Redux, created and maintained by the Redux team.

You might be wondering why the Redux team created Redux Toolkit in the first place. The answer is right at the beginning of the documentation:

The Redux Toolkit package is intended to be the standard way to write Redux logic. It was originally created to help address three common concerns about Redux:

- "Configuring a Redux store is too complicated"
- "I have to add a lot of packages to get Redux to do anything useful"
- "Redux requires too much boilerplate code"
All-in-all, the whole point of using Redux Toolkit is to make using Redux easier and more efficient.

How to Install Redux Toolkit
You can install Redux Toolkit either with npm or yarn like this:

npm install @reduxjs/toolkit
yard add @reduxjs/toolkit
How to Use Redux Toolkit
The first stop when creating a Redux store is to set up state handling.

Instead of using reducers, we'll use createSlice which is provided by Redux Toolkit.

createSlice accepts 3 mandatory arguments, which are:

name: the name of the slice
initialState: the initial state of the slice (This is like the initial state of a reducer)
reducers: functions that affect the states (actions)
A slice, in brief, is like a modularized bundler of states and their actions.

Let's begin by creating a slice for authentication and its related actions:

const authSlice = createSlice({
  name: 'Auth', // Could name it anything
  initialState: {
    isLoggedIn: false,
  },
  reducers: {
    login: (state) => {
      state.isLoggedIn = true
    },
    logout: (state) => {
      state.isLoggedIn = false
    },
  },
})
auth-slice.js
As you can see, the slice above is dedicated to user authentication. initialState is an object that contains different initial state values, and the states that the reducers need to act upon. The functions in reducers are like regular reducers that accept state and action as arguments.

One noticeable catch in the above snippet is how we're are dealing with updating the state with state.isLoggedIn = true. This is clearly mutating the state, violating the property of immutability.

Or, is it?

Let's understand what's happening under the hood before jumping to conclusions.

On the surface it seems like we're mutating the existing state. But mutating the existing state wouldn't trigger the broadcasting of updated value(s) to the subscribers.

So when we use mutating syntax in the reduces, createSlice uses a library called Immer internally. This library draws the difference in the existing and the updated state, and reconstructs a new object with an updated value.

This means that, when we mutate the state, Immer takes care of building a new object and conforming to the property of immutability, making it easier for us to write code.

Now, let's write the slices for the other states:

const cartSlice = createSlice({
  name: 'Cart',
  initialState: {
    cart: [],
    qty: 0,
    total: 0,
  },
  reducers: {
    addItem: (state, action) => {
      state.cart.push(action.payload.cartItem)
      state.qty += action.payload.cartItem.qty
      state.total += action.payload.cartItem.price * action.payload.cartItem.qty
    },
    removeItem: (state, action) => {
      const itemIndex = state.cart.findIndex(
        (obj) => obj.id === action.payload.id
      )
      if (itemIndex !== -1 && state.cart[itemIndex].qty >= 1) {
        state.cart[itemIndex].qty -= 1
      }
    }
  }
})
cart-slice.js
