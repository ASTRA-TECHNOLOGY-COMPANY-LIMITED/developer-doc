# Redux Toolkit App Structure

# Cre.

[Redux Essentials, Part 2: Redux Toolkit App Structure | Redux](https://redux.js.org/tutorials/essentials/part-2-app-structure)

# Redux Toolkit App Structure

## Redux Toolkit Purpose

- Making Redux syntax less complex, faster to write,…

## `configureStore`

```jsx
import { configureStore } from '@reduxjs/toolkit'
import counterReducer from '../features/counter/counterSlice'

export default configureStore({
  reducer: {
    counter: counterReducer
  }
})
```

- `**configureStore` is a function that helps us to create `store` with its own `reducers`.**
    - `configureStore` requires  that we pass in a `reducer` argument.
- When we pass in an object like `{counter: counterReducer}`, that says that
    - we want to have a `state.counter` section of our Redux state object,
    - and that we want the `counterReducer` function to be in charge of deciding if and how to update the `state.counter` section whenever an action is dispatched.
- `configureStore` automatically
    - adds several middleware to the store setup by default to provide a good developer experience,
    - and also sets up the store so that the Redux DevTools Extension can inspect its contents.

## `combineReducers`

- A Redux store needs to have a single "root reducer" function passed in when it's created.
- So if we have many different slice reducer functions, how do we get a single root reducer instead, and how does this define the contents of the
    
    ```jsx
    function rootReducer(state = {}, action) {
      return {
        users: usersReducer(state.users, action),
        posts: postsReducer(state.posts, action),
        comments: commentsReducer(state.comments, action)
      }
    }
    ```
    
    - **That**
        - **calls each slice reducer individually,**
        - **passes in the specific slice of the Redux state,**
        - **and includes each return value in the final new Redux state object.**
- **Redux has a function called `combineReducers` that does this for us automatically.**
- It accepts an object full of slice reducers as its argument, and returns a function that calls each slice reducer whenever an action is dispatched.
- The result from each slice reducer are all combined together into a single object as the final result.
- We can do the same thing as the previous example using `combineReducers`:
    
    ```jsx
    const rootReducer = combineReducers({
      users: usersReducer,
      posts: postsReducer,
      comments: commentsReducer
    })
    //----------------------------------------------
    const store = configureStore({
      reducer: rootReducer
    })
    ```
    

## `createSlice`

```jsx
import { createSlice } from '@reduxjs/toolkit'

export const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0
  },
  reducers: {
    increment: state => {
      state.value += 1
    },
    decrement: state => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload
    }
  }
})

export const { increment, decrement, incrementByAmount } = counterSlice.actions

export default counterSlice.reducer
```

- `**createSlice` is a function that help us**
    - **create a series of actions for a single feature in your app**
    - **create a reducer for those actions**
    - **create the initial state for the reducers**
- We have 3 actions that was created by `createSlice` function
    - `{type: "counter/increment"}`
    - `{type: "counter/decrement"}`
    - `{type: "counter/incrementByAmount"}`
- `createSlice` automatically generates action creators with the same names as the reducer functions we wrote.
    
    ```jsx
    console.log(counterSlice.actions.increment())// {type: "counter/increment"}
    ```
    
- `createSlice` uses a library called Immer inside so you can mutate state instead of writing immuatable updates by hand.

```jsx
function handwrittenReducer(state, action) {
  return {
    ...state,
    first: {
      ...state.first,
      second: {
        ...state.first.second,
        [action.someId]: {
          ...state.first.second[action.someId],
          fourth: action.someValue
        }
      }
    }
  }
}
```

```jsx
function reducerWithImmer(state, action) {
  state.first.second[action.someId].fourth = action.someValue
}
```

## **Writing Async Logic with Thunks**

A **thunk** is a specific kind of Redux function that can contain asynchronous logic. Thunks are written using two functions:

- An inside thunk function, which gets `dispatch` and `getState` as arguments
- The outside creator function, which creates and returns the inside thunk function
    
    ```jsx
    export const incrementAsync = amount => (dispatch, getState) => {
      setTimeout(() => {
        dispatch(incrementByAmount(amount))
      }, 1000)
    }
    
    //--------------------------------------------------------------
    const outsideThunk = (amount) => {
    	const insideThunk = (dispatch, getState) => {
    	  setTimeout(() => {
    	    dispatch(incrementByAmount(amount))
    	  }, 1000)
    	}
    	return insideThunk;
    }
    
    const incrementAsync = outsideThunk;
    ```
    

We can use them the same way we use a typical Redux action creator:

```jsx
store.dispatch(incrementAsync(5))
```

However, using thunks requires that the `redux-thunk` *middleware* (a type of plugin for Redux) be added to the Redux store when it's created. Fortunately, Redux Toolkit's `configureStore` function already sets that up for us automatically, so we can go ahead and use thunks here.

## Component

```jsx
import React, { useState } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import {
  decrement,
  increment,
  incrementByAmount,
  incrementAsync,
  selectCount
} from './counterSlice'
import styles from './Counter.module.css'

export function Counter() {
  const count = useSelector(selectCount)
  const dispatch = useDispatch()
  const [incrementAmount, setIncrementAmount] = useState('2')

  return (
    <div>
      <div className={styles.row}>
        <button
          className={styles.button}
          aria-label="Increment value"
          onClick={() => dispatch(increment())}
        >
          +
        </button>
        <span className={styles.value}>{count}</span>
        <button
          className={styles.button}
          aria-label="Decrement value"
          onClick={() => dispatch(decrement())}
        >
          -
        </button>
      </div>
      {/* omit additional rendering output here */}
    </div>
  )
}
```

## `useSelector`

- First, the `useSelector` hook lets our component extract whatever pieces of `state` from `store`.
    - Earlier, we have a function `store.getState()` that returns for us the `state`. But our component can't talk to the Redux store directly → `useSelector` was created to help us get the state from the store.
    
    ```jsx
    export const selectCount = state => state.counter.value
    
    //Wrong
    const count = selectCount(store.getState())
    console.log(count)
    
    //Right
    const count = useSelector(selectCount)
    ```
    
- We don't have to *only* use selectors that have already been exported, either. For example, we could write a selector function as an inline argument to `useSelector`
    
    ```jsx
    const countPlusTwo = useSelector(state => state.counter.value + 2)
    ```
    

## `useDispatch`

- Similarly, we know that if we had access to a Redux store, we could dispatch actions using action creators, like `store.dispatch(increment())`. Since we don't have access to the store itself, we need some way to have access to just the `dispatch` method → `useDispatch` was created.

## **Component State and Redux State**

- By now you might be wondering, "Do I always have to put all my app's state into the Redux store?"
- **In a React + Redux app, your global state should go in the Redux store, and your local state should stay in React components.**
- If you're not sure where to put something, here are some common rules of thumb for determining what kind of data should be put into Redux:
    - Do other parts of the application care about this data?
    - Do you need to be able to create further derived data based on this original data?
    - Is the same data being used to drive multiple components?
    - Is there value to you in being able to restore this state to a given point in time (ie, time travel debugging)?
    - Do you want to cache the data (ie, use what's in state if it's already there instead of re-requesting it)?
    - Do you want to keep this data consistent while hot-reloading UI components (which may lose their internal state when swapped)?
- **Most form state probably shouldn't be kept in Redux.** Instead, keep the data in your form components as you're editing it, and then dispatch Redux actions to update the store when the user is done.

## **Providing the Store**

```jsx
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'
import App from './App'
import store from './app/store'
import { Provider } from 'react-redux'
import * as serviceWorker from './serviceWorker'

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

## `nanoid()`

- A function helps us automatically create an random id.

```jsx
const onSavePostClicked = () => {
    if (title && content) {
      dispatch(
        postAdded({
          id: nanoid(),
          title,
          content
        })
      )

      setTitle('')
      setContent('')
    }
  }
```