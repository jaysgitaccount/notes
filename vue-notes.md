# Vue Notes

## Introduction
From [here](https://vuejs.org/guide/introduction.html#what-is-vue)

- JS framework for building UI
- Declarative, component based

Example:

JS:
    import { createApp } from 'vue'

    createApp({
        data() {
            return {
                count: 0
            }
        }
    }).mount('#app')

Template:

    <div id="app">
        <button @click="count++">
            Count is: {{ count }}
        </button>
    </div>

This demonstrates 2 core features of Vue:
- Declarative rendering: standard HTML is extended with a template syntax; allows HTML output based on JS state
- Reactivity: automatically tracks JS state and updates DOM accordingly

### Single-File Components
SFC (aka `.vue` files), encapsulate the logic(JS) of a component, template(HTML), and style(CSS) in one file.

The previous example, written in SFC format:

    <script>
    export default {
        data() {
            return {
                count: 0
            }
        }
    }
    </script>

    <template>
        <button @click="count++">Count is: {{ count }}</button>
    </template>

    <style scoped>
    button {
        font-weight: bold;
    }
    </style>

This is the recommended way to author Vue components if your case warrants a build setup.

### API Styles
Vue components can be authored in 2 different styles: Options API and Composition API.

#### Options API
Define component logic using an object of options e.g `data`, `methods`, `mounted`. Properties defined by options are exposed on `this` inside functions, pointing to the component instance:

    <script>
    export default {
        // Properties returned from data() become reactive state
        // and will be exposed on `this`
        data() {
            return {
                count: 0
            }
        },

        // Methods are functions that mutate state and trigger updates.
        // Can be bound as event listeners in templates.
        methods: {
            increment() {
                this.count++
            }
        },

        // Lifecycle hooks are called at different stages of a component's lifecycle.
        // This function will be called when the component is mounted.
        mounted() {
            console.log(`The initial count is ${this.count}.`)
        }
    }
    </script>

    <template>
         <button @click="increment">Count is: {{ count }}</button>
    </template>

#### Composition API
Define a component's logic using imported API functions. In SFC, Composition API is typically used with `<script setup>`. The `setup` attribute is a hint that makes Vue perform compile-time transforms that allow us to use Composition API with less boilerplate.

e.g. Imports and top-level variables/functions declared in `<script setup>` are directly usable in the template.

The same component with the same template, using Composition API and `<script setup>` instead:

    <script setup>
    import { ref, onMounted } from 'vue'

    // reactive state
    const count = ref(0)

    // functions that mutate state and trigger updates
    function increment() {
        count.value++
    }

    // lifecycle hooks
    onMounted(() => {
        console.log(`The initial count is ${count.value}.`)
    })
    </script>

    <template>
        <button @click="increment">Count is: {{ count }}</button>
    </template>

#### Which to choose?
Both styles are capable of covering common use cases; they are both powered by the same system. 

Options API is centered around a "component instance" (`this` as seen in the example). This typically aligns better with a class-based mental model for users coming from OOP language backgrounds.

It's also more beginner-friendly, abstracting away the reactivity details and enforcing code organisation via option groups.

The Composition API is centered around declaring reactive state variables directly in a function scope, and composing state from multiple functions together to handle complexity. It's more free-form and requires an understanding of how reactivity works in Vue, but also more flexible and powerful.

- Go with Options API if you are not using build tools, or plan to use Vue primarily in low-complexity scenarios, e.g. progressive enhancement.
- Go with Composition API + Single-File Components if you plan to build full applications with Vue.

## Tutorial Notes
Declare reactive state using the `data()` component option, a function that *returns* an object.
export default {
  data() {
    return {
      message: 'Hello World!'
    }
  }
}

### Attribute bindings
Mustaches: `{{  }}` are only used for text interpolation. To bind an attribute to a dynamic value, use the directive `v-bind`:
    <div v-bind:id="dynamicId"></div>

A directive is a special attribute that starts with `v-`. They are part of Vue's template syntax.

Similar to text interpolations, directive values are JS expressions that have access to the component's **state**.

The part after the colon, `:id`, is the *argument* of the directive. From this, the element's `id` will be synced with the `dynamicId` property from the component's state.

`v-bind` is used so frequently that it has a dedicated shorthand syntax:
    <div :id="dynamicId"></div>

You can use this for styling.

### Event Listeners
Listen to DOM events using `v-on`:

    <button v-on:click="increment">{{ count }}</button>

This also has a shorthand, `@`:

    <button @click="increment">{{ count }}</button>

(where `increment` refers to a function declares using the `methods` option)

(don't forget you can reference the component instance using `this`)

### Forming Bindings
Using `v-bind` and `v-on`, you can create 2-way bindings on form input elements:

    <script>
    export default {
        data() {
            return {
                text: ''
            }
        },
        methods: {
            onInput(e) {
                // a v-on handler receives the native DOM event
                // as the argument.
                this.text = e.target.value
            }
        }
    }
    </script>
    <template>
        <input v-model="text" placeholder="Type here">
        <p>{{ text }}</p>
    </template>

To simplify two-way bindings, Vue provides a directive `v-model` which is syntax sugar for `<input v-model="text">`

`v-model` automatically syncs the `<input>`'s value with the bound state. So, you don't need an event handler for that now.

It works on most (all??) input types e.g. checkboxes, radio buttons, dropdowns. I guess for all inputs, `v-model` removes the need for event listeners.

### Conditional Rendering
Use the `v-if` directive to conditionally render an element.

    <h1 v-if="awesome">Vue is awesome!</h1>

This `<h1>` will be rendered only if the value of awesome is truthy. If awesome changes to a falsy value, it will be removed from the DOM.

You can also use `v-else` and `v-else-if` to demote other branches of the condition.

    <h1 v-if="awesome">Vue is awesome!</h1>
    <h1 v-else>Oh no 😢</h1>

### List Rendering
Use the directive `v-for` to render a list of elements based on a source array.

    <ul>
        <li v-for="todo in todos" :key="todo.id">
            {{ todo.text }}
        </li>
    </ul>

Here, `todo` is a local variable representing the array element it's currently being iterated on. It's only accessible on/inside the `v-for` element (like function scope).

In the above example, each todo object is given a unique `id`, which is bound as the *special key attribute* for each `<li>`. The [`key`](https://vuejs.org/api/built-in-special-attributes.html#key) allows Vue to accurately move each `<li>` to match the position of its corresponding object in the array.

Two ways to update the list:
1. Call *mutating methods* on the source array: `this.todos.push(newTodo)`
2. Replace the array with a new one: `this.todos = this.todos.filter(/*...*/)`

#### `key` attribute
Children of the same parent must have unique `key` values; duplicate values will cause render errors.

### Computed Property
Using the `computed` option, you can declare a property that is reactively computed from other properties.

    export default {
        // data(), methods, ...
        computed: {
            filteredTodos() {
            // return filtered todos based on `this.hideCompleted`
            }
        }
    }

diff:

    - <li v-for="todo in todos">
    + <li v-for="todo in filteredTodos">

A computed property tracks other reactive state used in its computation as dependencies, caches the result, and automatically updates it when the dependencies change.

### wat
In the Vue tutorial, this:
    if (this.hideCompleted) {
       	return this.todos.filter(i => {i.done !== true} )
    } else if (!this.hideCompleted) {
        return this.todos;
    }
doesn't work. But this:
    if (this.hideCompleted) {
       	return this.todos.filter(i => i.done !== true )
    } else if (!this.hideCompleted) {
        return this.todos;
    }
Behaves as expected?

### Lifecycle and Template Refs
Sometimes, Vue won't be able to handle DOM updates for you, so you'll have to do it manually. In these cases, you can request a  **template ref** - a reference to an element in the template - using the special `ref` attribute: `<p ref="p">hello</p>`

The element will be exposed on `this.$refs` as `this.$refs.p`, but you can only access it after the component is mounted.

To run code after mount, use the `mounted` option.

This is a **lifecycle hook**; it allows us to register a callback at certain times of the component's lifecycle. There are other hooks, e.g. `created` and `updated`.

### Watchers
The option `watch` allows side effects to be performed reactively. e.g. logging a number to the console when it changes.

    watch: {
        count(newCount) {
            console.log(`new count is: ${newCount}`)        }
    }

The watch callback is called when a `count` changes, and *receives the new value as the argument* (in this case, we've called it `newCount`). 

### Components
Vue applications are created by nesting components. A parent component can render another component in its template as a child component.

First, import the child. Then register the component using the `components` option. Here we're using the object property shorthand to register the `ChildComp` component under the `ChildComp` key.

    import ChildComp from './ChildComp.vue'

    export default {
        components: {
            ChildComp
        }
    }

Then, use the component in the template as `<ChildComp />`.

### Props
A child component accepts input from the parent via **props**. First, we need to declare what props it accepts:

    export default {
        props: {
            msg: String
        }
    }

Once declared, `msg` is exposed on `this` and can be used in the child component's template.

The parent can pass the prop to the child just like attributes. To pass a dynamic value, you can also use `v-bind` syntax in the parent's template.

    <ChildComp :msg="greeting" />

### Emits
A child component can also *emit* events to the parent:

    export default {
        // declare emitted events
        emits: ['response']
        created() {
            // emit with argument
            this.$emit('response', 'hello from child')
        }
    }

The first argument to `this.$emit` is the event name. Any additional arguments are passed on to the event listener.

The parents can listen to these emitted events with `v-on`. The handler receives the extra argument from the child emit call and assigns it to local state.

    <ChildComp @response="(msg) => childMsg = msg" />

### Slots
The parent can also pass down template fragments to the child via *slots*.

    <ChildComp>
        This is some slot content!
    </ChildComp>

The child template can render the slot content from the parent using the `<slot/>` element.

Content in the slot outlet will be treated as fallback content; it is displayed if the parent did not pass down any slot content.

    <slot>Fallback content</slot>

