# REDUX PERSIST

- Redux persist là gì ?

  - Redux persist là một thư viện giúp cho người dùng có thể lưu trạng thái của Redux vào bộ nhớ cục bộ (Local Storage)

- Tác dụng của Redux pesist
  - Khi bạn phát triển một ứng dụng redux, trạng thái của ứng dụng thường được quản lý trong store và khi chúng ta tắt trình duyệt, tắt tab, hoặc làm mới web thì trạng thái trên Redux sẽ bị reset về state ban đầu. Redux persist được sinh ra để cho phép người dùng xử lý những vấn đề trên để mà không bị mất dữ liệu.
  - Tăng trải nghiệm cho người dùng.
  - Quản lý trạng thái cho những ứng dụng chứa dữ liệu lớn.

## Basic usage

- configureStore.js

```jsx
import { createStore } from "redux";
import { persistStore, persistReducer } from "redux-persist";
import storage from "redux-persist/lib/storage"; // defaults to localStorage for web

import rootReducer from "./reducers";

const persistConfig = {
  key: "root",
  storage,
};

const persistedReducer = persistReducer(persistConfig, rootReducer);

export default () => {
  let store = createStore(persistedReducer);
  let persistor = persistStore(store);
  return { store, persistor };
};
```

- App

```jsx
import { PersistGate } from "redux-persist/integration/react";

// ... normal setup, create store and persistor, import components etc.

const App = () => {
  return (
    <Provider store={store}>
      <PersistGate loading={null} persistor={persistor}>
        <RootComponent />
      </PersistGate>
    </Provider>
  );
};
```

## Black list and white list

```jsx
// BLACKLIST
const persistConfig = {
  key: "root",
  storage: storage,
  blacklist: ["navigation"], // navigation will not be persisted
};

// WHITELIST
const persistConfig = {
  key: "root",
  storage: storage,
  whitelist: ["navigation"], // only navigation will be persisted
};
```

## Nested Persists

- Sử dụng khi cần reset một case nào đó trong nested reducer

```jsx
import { combineReducers } from "redux";
import { persistReducer } from "redux-persist";
import storage from "redux-persist/lib/storage";

import { authReducer, otherReducer } from "./reducers";

const rootPersistConfig = {
  key: "root",
  storage: storage,
  blacklist: ["auth"],
};

const authPersistConfig = {
  key: "auth",
  storage: storage,
  blacklist: ["somethingTemporary"],
};

const rootReducer = combineReducers({
  auth: persistReducer(authPersistConfig, authReducer),
  other: otherReducer,
});

export default persistReducer(rootPersistConfig, rootReducer);
```

- Tuy nhiên với những trường hợp cần pesist vào level sâu hơn, thì đoạn code trên đối với tôi phải setup rất dài dòng và có thể gây nhầm lẫn. Vậy nên chúng ta có thể cài thêm các thư viện khác để reset các trường trong level sâu hơn như sau

  - Thư viện:
    - lodash: thư viện giúp xử lý và thao tác với dữ liệu
    - history: quản lý lịch sử của web
    - connected-react-router: thư viện kết nối giữa react router và redux

- store.ts

```jsx
import { configureStore } from '@reduxjs/toolkit';
import { setupListeners } from '@reduxjs/toolkit/query';
import rootReducer from '../reducer/rootReducer';
import { persistStore, persistReducer, createTransform } from 'redux-persist';
import storage from 'redux-persist/lib/storage';
import apiSlice from '../slice/apiSlice';
import omit from 'lodash/omit';

// black list paths
const blacklistPaths: any[] = [
    'layout.pageUrl.currentPage'
];

// Creating Persisted Reducer
const persistedReducer = persistReducer(
  {
    key: 'root',
    storage,
    // Optionally, you can add a whitelist or blacklist to choose what to persist
    whitelist: ['auth', 'layout'],
    blacklist: blacklistPaths.filter((a) => !a.includes('.')),
    transforms: [
      // nested blacklist-paths require a custom transform to be applied
      createTransform((inboundState: any, key: any) => {
        const blacklistPaths_forKey = blacklistPaths
          .filter((path) => path.startsWith(`${key}.`))
          .map((path) => path.substr(key.length + 1));
        return omit(inboundState, ...blacklistPaths_forKey);
      }, null)
    ]
  },
  rootReducer(history as any) as any
);

// Store Configuration
export const store = configureStore({
  reducer: {
    // Combine persisted reducer with apiSlice.reducer
    root: persistedReducer,
    [apiSlice.reducerPath]: apiSlice.reducer
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      serializableCheck: {
        // Ignore these action types in the serializable check
        ignoredActions: ['persist/PERSIST', 'persist/REHYDRATE', 'persist/PURGE']
      }
    }).concat(apiSlice.middleware),
  devTools: import.meta.env.VITE_ENVIRONMENT !== 'production'
});

// Persistor
export const persistor = persistStore(store);

// Setup Listeners
setupListeners(store.dispatch);
```

- rootReducer.ts

```jsx
import { combineReducers } from "redux";
import { History } from "history";
import { connectRouter } from "connected-react-router";
import layoutReducer from "#/modules/common/reducer/layout.reducer";
import authReducer from "#/modules/auth/reducer/auth.reducer";
import performanceReducer from "#/modules/performance/reducer/performance.reducer";
import docaiReducer from "#/modules/docai/reducer/docai.reducer";

export default (history: History) =>
  combineReducers({
    router: connectRouter(history),
    layout: layoutReducer,
    performance: performanceReducer,
    auth: authReducer,
    docai: docaiReducer,
  });
```
