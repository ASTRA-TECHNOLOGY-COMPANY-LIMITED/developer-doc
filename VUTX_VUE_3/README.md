# Vue 3

## Require

- Vite requires Node.js version 18+. 20+.

## Create project

- yarn create vite my-vue-app --template vue-ts

## Install package

- vue-router
- yup, vee-validate (optional)
- pinia, pinia-plugin-persistedstate (optional)
- axios, tailwind, firebase, vue3-toastify,...

## Start project

- npm run dev / yarn dev

## Vue file structure

```html
<template> Your html ui</template>

<script lang="ts">
  Your logic code
</script>

<style scoped></style>
```

## Script vs script setup, I will use script setup for this project

- Script

```html
<script>
  export default {
    data() {
      return {
        items: [
          { id: 1, name: "Item 1" },
          { id: 2, name: "Item 2" },
          { id: 3, name: "Item 3" },
        ],
      };
    },
    methods: {
      addItem() {
        const newItem = {
          id: this.items.length + 1,
          name: `Item ${this.items.length + 1}`,
        };
        this.items.push(newItem);
      },
    },
  };
</script>
```

- Script setup

```html
<script setup lang="ts">
  import { ref } from "vue";

  const items = ref([
    { id: 1, name: "Item 1" },
    { id: 2, name: "Item 2" },
    { id: 3, name: "Item 3" },
  ]);

  const addItem = () => {
    const newItem = {
      id: items.value.length + 1,
      name: `Item ${items.value.length + 1}`,
    };
    items.value.push(newItem);
  };
</script>
```

## Reactivity core

```jsx
import { ref, reactive } from "vue";

// single state
const numberState = ref(0);

// multiple state
const userState = reactive({
  id:'190ff08c-2504-4ce6-8667-b760423bec97'
  name: "user",
  phone: "19001009",
  email: "use@gmail.com",
});

// computed
// readonly computed ref
const count = ref(1)
const plusOne = computed(() => count.value + 1)
console.log(plusOne.value) // 2

// read and write computed ref
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: (val) => {
    count.value = val - 1
  }
})

plusOne.value = 1
console.log(count.value) // 0

// readonly
const copy = readonly(1)
```

- Watch

```html
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>
<script setup>
  import {
    ref,
    watch,
    watchEffect,
    watchPostEffect,
    watchSyncEffect,
  } from "vue";

  // Define a reactive variable
  const count = ref(0);

  // Function to increment count
  const increment = () => {
    count.value++;
  };

  // Watch: Logs when count changes
  watch(count, (newValue, oldValue) => {
    console.log(`watch - count has changed from ${oldValue} to ${newValue}`);
  });

  // WatchEffect: Logs the current value of count
  watchEffect(() => {
    console.log(`watchEffect - count is currently ${count.value}`);
  });

  // WatchPostEffect: Logs the current value of count after each render
  watchPostEffect(() => {
    console.log(
      `watchPostEffect - count is currently ${count.value} (after render)`
    );
  });

  // WatchSyncEffect: Logs the current value of count synchronously with set
  watchSyncEffect(() => {
    console.log(
      `watchSyncEffect - count is currently ${count.value} (sync with set)`
    );
  });
</script>
```

- WatchEffect: Runs asynchronously after the render cycle, suitable for most side effects that don't require immediate synchronization with data mutations.
- WatchSyncEffect: Runs synchronously with the data mutation that triggered it, ensuring immediate synchronization with reactive data changes. Ideal for side effects that need to be executed immediately after reactive data mutations.
- In summary, watchSyncEffect provides a way to perform synchronous side

## Vue Lifecycle Hook

- beforeCreate -> Use setup() instead:

  - In the options API, beforeCreate hook is called before the instance is initialized, at the earliest stage of the component's lifecycle.
  - In the Composition API, you can perform similar initialization logic using the setup() function.

- created -> Use setup() instead:

  - Similar to beforeCreate, created hook in the options API is called after the instance has been created but before the component is mounted to the DOM.
  - You can achieve the same behavior by performing initialization logic within the setup() function.

- onBeforeMount -> Before mounting DOM:

  - onBeforeMount hook is called right before the component is mounted to the DOM.
  - It's useful for performing tasks or setting up state that needs to be in place before the component is rendered.

- onMounted -> DOM can be accessed:

  - onMounted hook is called after the component has been mounted to the DOM.
  - It's the appropriate place to perform tasks that require access to the DOM elements.

- onBeforeUpdate -> Reactive data changes:

  - onBeforeUpdate hook is called before a component is re-rendered due to reactive data changes.
  - It's useful for performing tasks or calculations based on state changes before the component updates.

- onUpdated -> DOM has been updated:

  - onUpdated hook is called after the component has been re-rendered due to reactive data changes.
  - It's useful for performing tasks that need to be executed after the component has been updated and the DOM has been patched.

- onBeforeUnmount -> Component still complete:

  - onBeforeUnmount hook is called right before the component is unmounted.
  - It's useful for performing cleanup tasks or releasing resources before the component is removed from the DOM.

- onUnmounted -> Teardown complete:

  - onUnmounted hook is called after the component has been unmounted and its teardown is complete.
  - It's useful for performing final cleanup tasks or releasing any remaining resources associated with the component.

## Template syntax and Binding data

```html
<div :id="userState.id">{{userState.name}}</div>
```

## Conditional rendering

```html
<div v-if="date == today">...</div>
<div v-else-if="!done">...</div>
<div v-else>...</div>
```

## Event

```html
<button v-on:click="count"></button>

<button @click="count"></button>

<button @click.stop="count"></button>
```

- Modifiers

* .stop - call event.stopPropagation().
* .prevent - call event.preventDefault().
* .capture - add event listener in capture mode.
* .self - only trigger handler if event was dispatched from this element.
* .{keyAlias} - only trigger handler on certain keys.
* .once - trigger handler at most once.
* .left - only trigger handler for left button mouse events.
* .right - only trigger handler for right button mouse events.
* .middle - only trigger handler for middle button mouse events.
* .passive - attaches a DOM event with { passive: true }.

## List rendering

```html
<li v-for="(item, index) in items">{{ index }} : {{ item }}</li>
```

## Props

```html
<script setup lang="ts">
  import { defineProps, toRefs } from "vue";

  const props = defineProps<{
    title?: string;
    type: "button" | "submit" | "reset";
    isLoading?: boolean;
    className?: string;
  }>();

  const {
    type = "button",
    title = "submit",
    className = "",
    isLoading = false,
  } = toRefs(props);
</script>
```

## Router

```jsx
import { createRouter, createWebHistory } from "vue-router";
const routes = [
  {
    path: "/home",
    name: "HomePage",
    component: () => import("@/views/HomePage.vue"),
  },
  {
    path: "/login",
    name: "LoginPage",
    component: () => import("@/views/LoginPage.vue"),
  },
  {
    path: "/profile",
    name: "ProfilePage",
    component: () => import("@/views/ProfilePage.vue"),
    meta: {
      requiresAuth: true,
    },
  },
];

const router = createRouter({ history: createWebHistory(), routes });

// protect router
// can add more condition in meta to check
router.beforeEach((to, _, next) => {
  const auth = useAuthStore();
  const { login } = auth;
  if (to.matched.some((record) => record.meta.requiresAuth)) {
    if (login) {
      next();
    } else {
      next({ name: "LoginPage" });
    }
  } else {
    next();
  }
});
export default router;
```

## Navigate

```html
<script setup lang="ts">
  import { useRouter } from "vue-router";
  const router = useRouter();

  const navigate = () => {
    // literal string path
    router.push("/users/eduardo");

    // object with path
    router.push({ path: "/users/eduardo" });

    // named route with params to let the router build the url
    // path: '/user/:username',
    router.push({ name: "user", params: { username: "eduardo" } });

    // with query, resulting in /register?plan=private
    router.push({ path: "/register", query: { plan: "private" } });

    // with hash, resulting in /about#team
    router.push({ path: "/about", hash: "#team" });

    // with state through navigate
    router.push({
      path: "/your-route",
      state: { customData: "your custom data" }, // Custom state object
    });

    // how to access state through navigate
    console.log(window.history.state.customData);
  };
</script>

<template>
  <router-link to="/home">Home</router-link>
</template>
```

## Demo Project:

- Github: https://github.com/vutran221097/vue3

## Document

- Basic Vue3: https://learnvue.co/LearnVue-Vue-3-Cheatsheet.pdf

## Recommended IDE Setup

- [VS Code](https://code.visualstudio.com/)
- [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur) -
- [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin).
