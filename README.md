# hooks-cleanup

**To understand why we need `componentWillunmount` or `cleanup` we use in an example**

```js
import React, { useState } from 'react'

function MouseContainer() {
  const [display, setDisplay] = useState(false)
  
  return (
    <div>
      <button onClick={() => setDisplay(!display)}>toggle</button>
      {display && <Component />}
    </div>
  )
}
```
. `<Component />` is a component which when I hover the browser I see the `coordinates` in the dom

. We have a button to toggle the `display` state from false to true and true to false
. Now as you can see when we click the toggle button we `unmount` the `<Component />` from the dom.

. Now after I `unmounted` the `<Component />` from the dom, and now if I try to hover the browser or dom, I will get an error:

**ERROR**
`Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup function.`

If we console.log the event listener of the `<Component />` we can see that even though the component has been removed from the dom, the console.log shows the event listener of the `<Component />` still `listening` and `executing`. And this is what the warning indicates as well. 

**So we have to cleanup our component**
**How do we handle the cleanup code?**

Component.js (Class)
```js
logMousePosition = e => {
  this.setState({ x: e.clientX, y:e.clientY })
}

componentDidMount() {
  window.addEventListener('mousemove', this.logMousePosition)
}

componentWillUnmount() {
  window.removeEventListener('mousemove', this.logMousePosition)
}
```

**in Hooks you have to return an arrow function from `useEffect` to actually cleanup the listener**
Component.js (Hooks)
```js
import React, { useState, useEffect } from 'react'

function Component() {
  const [x, setX] = useState(0)
  const [y, setY] = useState(0)
  
  const logMousePosition = e => {
    console.log('Mouse Event')
    setX(e.clientX)
    setY(e.clientY)
  }
  
  useEffect(() => {
    console.log('useEffect called')
    window.addEventListener('mousemove', logMousePosition)
    
    return () => {
      console.log('cleanup')
      window.removeEventListener('mousemove', logMousePosition)
    }
  }, [])
  
  return (
    <div>
      Hooks X - {x} Y - {y}
    </div>
  )
}
```

**So when you want to execute some cleanup code, you include it in a function, and return that function from the function passed to useEffect**
