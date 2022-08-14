# Using a State Reducer with Hooks
Ok, so the concept goes like this:

End user does an action
- Dev calls dispatch
- Hook determines the necessary changes
- Hook calls dev's code for further changes 👈 this is the inversion of control part
- Hook makes the state changes
## WARNING: Contrived example ahead: To keep things simple, I'm going to use a simple useToggle hook and component as a starting point. It'll feel contrived, but I don't want you to get distracted by a complicated example as I teach you how to use this pattern with hooks. Just know that this pattern works best when it's applied to complex hooks and components (like downshift).


```javascript

function useToggle() {
  const [on, setOnState] = React.useState(false)

  const toggle = () => setOnState(o => !o)
  const setOn = () => setOnState(true)
  const setOff = () => setOnState(false)

  return {on, toggle, setOn, setOff}
}

function Toggle() {
  const {on, toggle, setOn, setOff} = useToggle()

  return (
    <div>
      <button onClick={setOff}>Switch Off</button>
      <button onClick={setOn}>Switch On</button>
      <Switch on={on} onClick={toggle} />
    </div>
  )
}

function App() {
  return <Toggle />
}

ReactDOM.render(<App />, document.getElementById('root'))
Now, let's say we wanted to adjust the <Toggle /> component so the user couldn't click the <Switch /> more than 4 times in a row unless they click a "Reset" button:

function Toggle() {
  const [clicksSinceReset, setClicksSinceReset] = React.useState(0)
  const tooManyClicks = clicksSinceReset >= 4

  const {on, toggle, setOn, setOff} = useToggle()

  function handleClick() {
    toggle()
    setClicksSinceReset(count => count + 1)
  }

  return (
    <div>
      <button onClick={setOff}>Switch Off</button>
      <button onClick={setOn}>Switch On</button>
      <Switch on={on} onClick={handleClick} />
      {tooManyClicks ? (
        <button onClick={() => setClicksSinceReset(0)}>Reset</button>
      ) : null}
    </div>
  )
}

```

# Cool, so an easy solution to this problem would be to add an if statement in the handleClick function and not call toggle if tooManyClicks is true, but let's keep going for the purposes of this example.

How could we change the useToggle hook, to invert control in this situation? Let's think about the API first, then the implementation second. As a user, it'd be cool if I could hook into every state update before it actually happens and modify it, like so:

```javascrip
function Toggle() {
  const [clicksSinceReset, setClicksSinceReset] = React.useState(0)
  const tooManyClicks = clicksSinceReset >= 4

  const {on, toggle, setOn, setOff} = useToggle({
    modifyStateChange(currentState, changes) {
      if (tooManyClicks) {
        // other changes are fine, but on needs to be unchanged
        return {...changes, on: currentState.on}
      } else {
        // the changes are fine
        return changes
      }
    },
  })

  function handleClick() {
    toggle()
    setClicksSinceReset(count => count + 1)
  }

  return (
    <div>
      <button onClick={setOff}>Switch Off</button>
      <button onClick={setOn}>Switch On</button>
      <Switch on={on} onClick={handleClick} />
      {tooManyClicks ? (
        <button onClick={() => setClicksSinceReset(0)}>Reset</button>
      ) : null}
    </div>
  )
}
```
So that's great, except it prevents changes from happening when people click the "Switch Off" or "Switch On" buttons, and we only want to prevent the <Switch /> from toggling the state.

Hmmm... What if we change modifyStateChange to be called reducer and it accepts an action as the second argument? Then the action could have a type that determines what type of change is happening, and we could get the changes from the toggleReducer which would be exported by our useToggle hook. We'll just say that the type for clicking the switch is TOGGLE.

function Toggle() {
  const [clicksSinceReset, setClicksSinceReset] = React.useState(0)
  const tooManyClicks = clicksSinceReset >= 4

  const {on, toggle, setOn, setOff} = useToggle({
    reducer(currentState, action) {
      const changes = toggleReducer(currentState, action)
      if (tooManyClicks && action.type === 'TOGGLE') {
        // other changes are fine, but on needs to be unchanged
        return {...changes, on: currentState.on}
      } else {
        // the changes are fine
        return changes
      }
    },
  })

  function handleClick() {
    toggle()
    setClicksSinceReset(count => count + 1)
  }

  return (
    <div>
      <button onClick={setOff}>Switch Off</button>
      <button onClick={setOn}>Switch On</button>
      <Switch on={on} onClick={handleClick} />
      {tooManyClicks ? (
        <button onClick={() => setClicksSinceReset(0)}>Reset</button>
      ) : null}
    </div>
  )
}
Nice! This gives us all kinds of control. One last thing, let's not bother with the string 'TOGGLE' for the type. Instead we'll have an object of all the change types that people can reference instead. This'll help avoid typos and improve editor autocompletion (for folks not using TypeScript):

function Toggle() {
  const [clicksSinceReset, setClicksSinceReset] = React.useState(0)
  const tooManyClicks = clicksSinceReset >= 4

  const {on, toggle, setOn, setOff} = useToggle({
    reducer(currentState, action) {
      const changes = toggleReducer(currenState, action)
      if (tooManyClicks && action.type === actionTypes.toggle) {
        // other changes are fine, but on needs to be unchanged
        return {...changes, on: currentState.on}
      } else {
        // the changes are fine
        return changes
      }
    },
  })

  function handleClick() {
    toggle()
    setClicksSinceReset(count => count + 1)
  }

  return (
    <div>
      <button onClick={setOff}>Switch Off</button>
      <button onClick={setOn}>Switch On</button>
      <Switch on={on} onClick={handleClick} />
      {tooManyClicks ? (
        <button onClick={() => setClicksSinceReset(0)}>Reset</button>
      ) : null}
    </div>
  )
}
Implementing a State Reducer with Hooks
Alright, I'm happy with the API we're exposing here. Let's take a look at how we could implement this with our useToggle hook. In case you forgot, here's the code for that:

function useToggle() {
  const [on, setOnState] = React.useState(false)

  const toggle = () => setOnState(o => !o)
  const setOn = () => setOnState(true)
  const setOff = () => setOnState(false)

  return {on, toggle, setOn, setOff}
}
We could add logic to every one of these helper functions, but I'm just going to skip ahead and tell you that this would be really annoying, even in this simple hook. Instead, we're going to rewrite this from useState to useReducer and that'll make our implementation a LOT easier:

function toggleReducer(state, action) {
  switch (action.type) {
    case 'TOGGLE': {
      return {on: !state.on}
    }
    case 'ON': {
      return {on: true}
    }
    case 'OFF': {
      return {on: false}
    }
    default: {
      throw new Error(`Unhandled type: ${action.type}`)
    }
  }
}

function useToggle() {
  const [{on}, dispatch] = React.useReducer(toggleReducer, {on: false})

  const toggle = () => dispatch({type: 'TOGGLE'})
  const setOn = () => dispatch({type: 'ON'})
  const setOff = () => dispatch({type: 'OFF'})

  return {on, toggle, setOn, setOff}
}
Ok, cool. Really quick, let's add that types property to our useToggle to avoid the strings thing. And we'll export that so users of our hook can reference them:

const actionTypes = {
  toggle: 'TOGGLE',
  on: 'ON',
  off: 'OFF',
}
function toggleReducer(state, action) {
  switch (action.type) {
    case actionTypes.toggle: {
      return {on: !state.on}
    }
    case actionTypes.on: {
      return {on: true}
    }
    case actionTypes.off: {
      return {on: false}
    }
    default: {
      throw new Error(`Unhandled type: ${action.type}`)
    }
  }
}

function useToggle() {
  const [{on}, dispatch] = React.useReducer(toggleReducer, {on: false})

  const toggle = () => dispatch({type: actionTypes.toggle})
  const setOn = () => dispatch({type: actionTypes.on})
  const setOff = () => dispatch({type: actionTypes.off})

  return {on, toggle, setOn, setOff}
}

export {useToggle, actionTypes}
Cool, so now, users are going to pass reducer as a configuration object to our useToggle function, so let's accept that:

function useToggle({reducer}) {
  const [{on}, dispatch] = React.useReducer(toggleReducer, {on: false})

  const toggle = () => dispatch({type: actionTypes.toggle})
  const setOn = () => dispatch({type: actionTypes.on})
  const setOff = () => dispatch({type: actionTypes.off})

  return {on, toggle, setOn, setOff}
}
Great, so now that we have the developer's reducer, how do we combine that with our reducer? Well, if we're truly going to invert control for the user of our hook, we don't want to call our own reducer. Instead, let's expose our own reducer and they can use it themselves if they want to, so let's export it, and then we'll use the reducer they give us instead of our own:

