# Introduction

## Vue

Vue provides a declarative, component-based programming model that helps you efficiently develop user interfaces of any complexity.

        import { createApp, ref } from 'vue'
        createApp({
            setup() {
                return {
                    count: ref(0)
                }
            }
        }).mount('#app')

Template:

        <div id="app">
            <button @click="count++">
                Count is: {{ count }}
            </button>
        </div>

The above example demonstrates the two core features of Vue:

- **Declarative Rendering:** Vue extends standard HTML with a template syntax that allows us to declaratively describe HTML output based on JavaScript state.

- **Reactivity:** Vue automatically tracks JavaScript state changes and efficiently updates the DOM when changes happen.

## The Progressive Framework

Vue can be used in different ways:

- Enhancing static HTML without a build step
- Embedding as Web Components on any page
- Single-Page Application (SPA)
- Fullstack / Server-Side Rendering (SSR)
- Jamstack / Static Site Generation (SSG)
- Targeting desktop, mobile, WebGL, and even the terminal

## Single-File Components

A Vue SFC, as the name suggests, encapsulates the component's logic (JavaScript), template (HTML), and styles (CSS) in a single file.

        <script setup>
        import { ref } from 'vue'
        const count = ref(0)
        </script>

        <template>
            <button @click="count++">Count is: {{ count }}</button>
        </template>

        <style scoped>
        button {
            font-weight: bold;
        }
        </style>

## API Styles

### Options API

With Options API, we define a component's logic using an object of options such as `data`, `methods`, and `mounted`. Properties defined by options are exposed on `this` inside functions, which points to the component instance:

        <script>
        export default {
        // Properties returned from data() become reactive state
        // and will be exposed on `this`.
        data() {
            return {
                count: 0
            }
        },

        // Methods are functions that mutate state and trigger updates.
        // They can be bound as event handlers in templates.
        methods: {
            increment() {
                this.count++
            }
        },

        // Lifecycle hooks are called at different stages
        // of a component's lifecycle.
        // This function will be called when the component is mounted.
        mounted() {
            console.log(`The initial count is ${this.count}.`)
        }
        }
        </script>

        <template>
            <button @click="increment">Count is: {{ count }}</button>
        </template>

### Composition API

With Composition API, we define a component's logic using imported API functions. In SFCs, Composition API is typically used with `<script setup>`. The `setup` attribute is a hint that makes Vue perform compile-time transforms that allow us to use Composition API with less boilerplate. For example, imports and top-level variables / functions declared in `<script setup>` are directly usable in the template.

Here is the same component, with the exact same template, but using Composition API and `<script setup>` instead:

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
            console.log(`The intial count is ${count.value}.`)
        })
        </script>

        <template>
            <button @click="increment">Count is: {{ count }}</button>
        </template>

## Which to Choose?

Both API styles are fully capable of covering common use cases. They are different interfaces powered by the exact same underlying system. In fact, the Options API is implemented on top of the Composition API!

- For production use:

  - Go with Options API if you are not using build tools, or plan to use Vue primarily in low-complexity scenarios, e.g. progressive enhancement.

  - Go with Composition API + Single-File Components if you plan to build full applications with Vue.
