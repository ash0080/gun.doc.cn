# Want to use GUN with Svelte?

They're a match made in heaven!

Check out [this simple example](https://svelte.dev/repl/634ff063c3424bb9a203bd38cbaa2d73?version=3.16.4) to see how easy it is to get started or follow the tutorial below.

The recommended approach to bind GUN data to Svelte is this:
```javascript
<script>
import { gun } from "./gunSetup.js" // Only initialize GUN once

// Initialize a variable to store data from GUN
let store = {}

// Set up a GUN listener to update the value
gun.get("cats").map().on(function(data, key) {
    if (data) {
        store[key] = data
    } else {
        // gun.map() can return null (deleted) values for keys
        // if so, this else clause will update your local variable
        delete store[key]
        store = store
    }
})

// Reshape data to convenient variables for the view
$: cats = Object.entries(store)
</script>

{#each cats as [key, cat]}
<div>{cat.name}</div>
{/each}
```

### About Svelte and GUN
Svelte has an [approach to reactivity](https://svelte.dev/blog/svelte-3-rethinking-reactivity) that couples very well with GUN, since you are able to use gun directly as a store within Svelte components. As a result, you can easily bind GUN data to Svelte in a way that feels like vanilla JavaScript, and is similar to the vanilla examples used in most of GUNs documentation.

Before we begin this tutorial you should be familiar with Svelte and its APIs, as we will only focus on the data binding to GUN.

## Tutorial
In this tutorial we will build a very basic todo application.

### Step 1 - Set up GUN

Let's create a new file called `initGun.js`. You want to place this in a separate file since you only want to initialize gun once. We'll import the gun object into the Svelte components from this file.

```javascript
// initGun.js
import Gun from "gun/gun"
export const gun = Gun()
```

```html
// App.svelte
<script>
import { gun } from "./initGun.js"
</script>
```

### Step 2 - Create an input to add Todos
```html
// App.svelte
<script>
// ...

let input = ""
const create = () => gun.get("todos").key(input).put({ title: input, done: false })
</script>

<input bind:value={input} on:change={e => create() && (e.target.value = "")} />
```

### Step 3 - List Todos from GUN
```html
// App.svelte
<script>
// ...

let store = {}
gun.get("todos").map().on(function(data, key) {
    if (data) {
        store[key] = data
    } else {
        // gun.map() can return null (deleted) values for keys
        // if so, this else clause will update your local variable
        delete store[key]
        store = store
    }
})

// This syntax should familiar to you if you completed the official Svelte tutorial
// We'll convert the store to a sorted list that can be iterated in the view
$: todos = Object.entries(store).sort((a, b) => a[1].done ? -1 : 1))
</script>

{#each todos as [key, todo]}
<div id={key}>
    {todo.title} {todo.done ? "😺" : "😾"}
</div>
{/each}
```

### Step 4 - Add update and delete actions
```html
<script>
// ...
const update = (key, value) => gun.get(key).get("done").put(value)
const remove = key => gun.get(key).put(null)
</script>

{#each todos as [key, todo]}
<div id={key}>
    <input type="checkbox" checked={todo.done} on:change={() => update(key, !todo.done)} />
    {todo.title} <a href="/" on:click|preventDefault={() => remove(key)}>remove</a> {todo.done ? "😺" : "😾"}
</div>
{/each}
```

## Using Svelte stores
While this approach increases the boilerplate of your application, you might find it necessary to create global stores that can be reused in multiple components.

### Simple example

```javascript
// stores.js
import { gun } from "./initGun.js"
import { writable } from "svelte/store"

function createStore(ref, initialValue) {
    const { set, subscribe } = writable(initialValue)
    ref.on(set)
    return {
        subscribe
    }
}

const todosRef = gun.get("todos")
export const todos = createStore(todosRef, {})
```

### Advanced example with methods
```javascript
// stores.js
import { gun } from "./initGun.js"
import { writable } from "svelte/store"

function createMapStore(ref) {
    const { update, subscribe } = writable({})
    ref.on(function(data, key) {
        if (data) {
            update(store => ({...store, [key]: data}))
        } else {
            update(store => {
                delete store[key]
                return store
            })
        }
    })        

    return {
        subscribe,
        update: (key, value) => ref.get(key).put(value)
    }
}

const todosRef = gun.get("todos").map()
export const todos = createMapStore(todosRef, {})
```

---
Anything to add? [Add more to this article](https://github.com/amark/gun/wiki/Svelte/_edit)! 