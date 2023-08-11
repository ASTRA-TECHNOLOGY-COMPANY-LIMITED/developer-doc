# Overview and Concepts

# What is Redux?

- **Redux is a pattern and library for managing and updating application state, using events call “actions”.**
- It stores all the states at a centralized store so states can be used across application.
- State can only be updated in a predictable fashion.

## Why should we use Redux?

- **Redux makes it easier to understand where, when, why, how the state is being updated, how our application logic works when those changes occur.**

## When shoud we use Redux?

- Redux helps us deal with shared state management.
- But there will be more concepts to learn, more code to write, more indirection (định hướng) which we have to follow.
- **Redux is more useful when**
    - **We have large amounts of state that are needed in many components**
    - **State is updated frequently over time**
    - **The logic to update state may be complex**
    - **Application might be worked on by many people**

## **Redux Libraries and Tools**

- There are a lot of libraries which related to Redux
    - **React Redux:** Connecting Redux (Managing state, handle logic) to React (View)
    - **Redux Toolkit:** Redux syntax is very complex, RTK makes it easier
    - **Redux DevTools Extension:** Showing us Redux Data Flow (action getting dispatched, state chaged,…)

# Redux Terms and Concepts

## State Management

- **Basic idea behind Redux: a single centralized place to contain the global state in your application, and specific patterns to follow when updating that state to make the code predictable.**
    - Normal data flow
        - States are being initialization.
        - UI is rendered based on those states
        - Event happens, state is changed
        - UI is re-rendered based on the new state
    - However, the simplicity can break down when we have **multiple components that need to share and use the same state**, especially if those components are located in different parts of the application.
    - The solution is extract all the state from the components, put it into a centralized store outside the component tree.
        - Component tree does not contain state → Only describe View
        - Any component can access the store, trigger (dispatch) actiopns
    - Redux is working based on that idea. It defines and separetes new concepts to divide view displaying and state management.
- If you're not sure where to put something, here are some common rules of thumb for determining what kind of data should be put into Redux:
    - Do other parts of the application care about this data?
    - Do you need to be able to create further derived data based on this original data?
    - Is the same data being used to drive multiple components?
    - Is there value to you in being able to restore this state to a given point in time (ie, time travel debugging)?
    - Do you want to cache the data (ie, use what's in state if it's already there instead of re-requesting it)?
    - Do you want to keep this data consistent while hot-reloading UI components (which may lose their internal state when swapped)?

## Immutability

- **Redux expects that all state updates are done immutably**.

## Terminology

### Redux

```jsx
//declare an action
const plusAction = {
  type: 'calculate/plus',
  payload: 2
}
//or
const increment = (n) => {return { type: 'calculate/plus', payload: n }}

//declare a reducer
const initialState = { value: 0 }
function calculationReducer(state = initialState, action) {
  if (action.type === 'calculate/plus') {
    return {
      ...state,
      value: state.value + action.payload
    }
  }

  // otherwise return the existing state unchanged
  return state
}

//create a store
import { configureStore } from '@reduxjs/toolkit'
const store = configureStore({ reducer: calculationReducer })

const selectCount = state => state.value
```

### React

```jsx

import { increment, selectCount } from '...';
import { useSelector, useDispatch } from 'react-redux'

const Componet = () => {
	const count = useSelector(selectCount)
  const dispatch = useDispatch()

	const handlePlusClick = () => {
		dispatch(increment(3)); //{ type: 'calculate/plus', payload: 3 }
		console.log(count) // 3
	}
	
	return ...;
}
```

### There are some important Redux terms that you need know

- **Actions**
    - Defination: Action is a normal object which represent for a event type, each action has its own handler.
    - Purpose: It helps Reducer using correct handler for correct action (event type).
        
        ```jsx
        const addTodoAction = {
          type: 'todos/todoAdded',
          payload: 'Buy milk'
        }
        ```
        
- **Action Creators**
    - Definition: **Action creator** is a function that creates an action.
    - Purpose: Don't have to write the action object by hand every time
        
        ```jsx
        const addTodo = text => {
          return {
            type: 'todos/todoAdded',
            payload: text
          }
        }
        ```
        
- **Reducers**
    - Definition: Reducer is a function that compute and return the new state. It receives 2 paramenters
        - `state`: our old state
        - `action`: action that occur the reducer.
        - Reducer is like an event handler that get called based on the received `action` type.
    - Rules
        - New state can only be calculated based on `state` and `action`
        - Can not modify the old state, must make immutable updates.
        - Can not cause “side effects”: asynchronous logic, calculate random values
    - How does reducer work
        - Check to see if the reducer cares about this action
            - If so, make a copy of the state, update the copy with new values, and return it
        - Otherwise, return the existing state unchanged
    - Example
        
        ```jsx
        const initialState = { value: 0 }
        
        function counterReducer(state = initialState, action) {
          // Check to see if the reducer cares about this action
          if (action.type === 'counter/increment') {
            // If so, make a copy of `state`
            return {
              ...state,
              // and update the copy with the new value
              value: state.value + 1
            }
          }
          // otherwise return the existing state unchanged
          return state
        }
        ```
        
- **Store**
    - Definition: Store is like a big React Context.
        - Store is created by passing in a reducer.
        - Store provides a method called `getState` that returns its state
        
        ```jsx
        import { configureStore } from '@reduxjs/toolkit'
        
        const store = configureStore({ reducer: counterReducer })
        
        console.log(store.getState())
        // {value: 0}
        ```
        
- **Dispatch**
    - Dispatch is a store’s method that trigger an action to update state.
    - **The only way to update the state is to call `store.dispatch()` and pass in an action object**.
    - After we dispatch an action,  store will run its reducer and save the new state, we can call `getState()` to retrieve the updated value.
    
    ```jsx
    //Code inside a component
    store.dispatch({ type: 'counter/increment' })
    
    console.log(store.getState())
    // {value: 1}
    ```
    
    - **You can think of dispatching actions as "triggering an event"** in the application. Something happened, and we want the store to know about it. Reducers act like event listeners, and when they hear an action they are interested in, they update the state in response.
    - We typically call action creators to dispatch the right action:
    
    ```jsx
    const increment = () => {return { type: 'counter/increment' }}
    store.dispatch(increment())
    console.log(store.getState())// {value: 2}
    ```
    
- **Selectors**
    - **Selector** is a normal function that helps us to get a part of store state instead of get a whole state.
        
        ```jsx
        const selectCounterValue = state => state.value
        
        const currentValue = selectCounterValue(store.getState())
        console.log(currentValue)
        ```
        
    - Components should always try to select the smallest possible amount of data they need from the store, which will help ensure that it only renders when it actually needs to.
        
        ```jsx
        //Wrong
        const postsList = useSelector(state => state.posts)
        const post = postsList.find(post => post.id === postId)
        
        //Right
        const post = useSelector(state =>
          state.posts.find(post => post.id === postId)
        )
        ```
        

## Redux Application Data Flow

- Initial setup
    - Redux create `store`, redux give `store` a main `reducer`.
    - `store` calls the `reducer` once, because there is no `action` provide to `reducer` so `reducer` return value as its initital `state`.
    - When the UI first render, UI accesses the current state of `store` and uses that data to render.
    - UI also subcribe to the `store` so they can know if the state has changed
        - (đăng ký nhận thông báo về sự thay đổi của state để có thể nhận biết được trong tương lai)
- Updates
    - Some event happens, like user clicks a button
    - Event handler dispatchs an action
    - Store runs the `reducer` function and give it current `state` and `action` that was dispatched, `reducer` returns the new `state`, overide the old `state`.
    - `store` notices all the UI that `state` has been updated
    - Each UI checks to see if the parts  of the state they need have changed?
    - Each component that sees its data has changed forces a re-render with a new data → View change ^^
    - Any time an action has been dispatched and the Redux store has been updated, `useSelector` will re-run our selector function. If the selector returns a different value than last time, `useSelector` will make sure our component re-renders with the new value.

![https://redux.js.org/assets/images/ReduxDataFlowDiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif](https://redux.js.org/assets/images/ReduxDataFlowDiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif)