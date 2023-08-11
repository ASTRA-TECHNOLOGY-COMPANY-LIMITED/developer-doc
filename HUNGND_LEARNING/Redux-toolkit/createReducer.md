## Cách dùng với "Builder Callback"

`createReducer` nhận vào 2 param

- **initialState** `State | () => State`: Là state khởi tạo hoặc một function khởi tạo state, function này khá hữu ích khi ta muốn lấy state từ localstorage
- **builderCallback** `(builder: Builder) => void`: Đây là callback nhận vào tham số là Builder object dùng để định nghĩa các case cho reducer

```ts
import {
  createAction,
  createReducer,
  AnyAction,
  PayloadAction
} from '@reduxjs/toolkit'

const increment = createAction<number>('increment')
const decrement = createAction<number>('decrement')

function isActionWithNumberPayload(
  action: AnyAction
): action is PayloadAction<number> {
  return typeof action.payload === 'number'
}

const reducer = createReducer(
  {
    counter: 0,
    sumOfNumberPayloads: 0,
    unhandledActions: 0
  },
  (builder) => {
    builder
      // Dùng addCase để thêm case trong trường hợp dùng createAction
      .addCase(increment, (state, action) => {
        // mutate state dễ dàng nhờ immer xử lý bên trong
        // không cần phải return state mới
        state.counter += action.payload
      })
      // Thêm case bằng cách dùng .addCase như muỗi chuỗi line
      .addCase(decrement, (state, action) => {
        state.counter -= action.payload
      })
      // addMatcher cho phép chúng ta thêm "matcher function"
      // nếu "matcher function" return true thì nó sẽ nhảy vào case này
      .addMatcher(isActionWithNumberPayload, (state, action) => {})
      // nếu muốn thêm default case khi không match case nào cả
      // thì dùng addDefaultCase
      .addDefaultCase((state, action) => {})
  }
)
```
