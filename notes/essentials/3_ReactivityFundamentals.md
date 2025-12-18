# Reactivity Fundamentals (Composition API)

## Declaring Reactive State

`### ref()`

In Composition API, the recommended way to declare reactive state is using the `ref()` function:

        import { ref } from 'vue'

        const count = ref(0)

`ref()` takes the argument and returns it wrapped within a ref object with a `.value` property:

        const count = ref(0)

        console.log(count) // { value: 0 }
        console.log(count.value) // 0

        count.value++
        console.log(count.value) // 1

To access refs in a component's template, declare and return them from a component's `setup()` function:

import { ref } from 'vue'

        export default {
        // `setup` is a special hook dedicated for the Composition API.
        setup() {
            const count = ref(0)

            // expose the ref to the template
            return {
            count
            }
        }
        }

Template:

        <div>{{ count }}</div>

Notice that we did **not** need to append `.value` when using the ref in the template. For convenience, refs are automatically unwrapped when used inside templates (with a few caveats).

You can also mutate a ref directly in event handlers:

        <button @click="count++">
        {{ count }}
        </button>

For more complex logic, we can declare functions that mutate refs in the same scope and expose them as methods alongside the state:

        import { ref } from 'vue'

        export default {
        setup() {
            const count = ref(0)

            function increment() {
            // .value is needed in JavaScript
            count.value++
            }

            // don't forget to expose the function as well.
            return {
            count,
            increment
            }
        }
        }

Exposed methods can then be used as event handlers:

        <button @click="increment">
            {{ count }}
        </button>

`### <script setup>`

Manually exposing state and methods via `setup()` can be verbose. Luckily, it can be avoided when using Single-File Components (SFCs). We can simplify the usage with `<script setup>`:

        <script setup>
        import { ref } from 'vue'

        const count = ref(0)

        function increment() {
            count.value++
        }
        </script>

        <template>
            <button @click="increment">
                {{ count }}
            </button>
        </template>

`### Why Refs?`

You might be wondering why we need refs with the `.value` instead of plain variables.

When you use a ref in a template, and change the ref's value later, Vue automatically detects the change and updates the DOM accordingly. This is made possible with a dependency-tracking based reactivity system. When a component is rendered for the first time, Vue **tracks** every ref that was used during the render. Later on, when a ref is mutated, it will **trigger** a re-render for components that are tracking it.

Conceptually, you can think of a ref as an object that looks like this:

        // pseudo code, not actual implementation
        const myRef = {
            _value: 0,
            get value() {
                track()
                return this._value
            },
            set value(newValue) {
                this._value = newValue
                trigger()
            }
        }

`### Deep Reactivity`

You can expect changes to be detected even when you mutate nested objects or arrays:

        import { ref } from 'vue'

        const obj = ref({
        nested: { count: 0 },
        arr: ['foo', 'bar']
        })

        function mutateDeeply() {
        // these will work as expected.
        obj.value.nested.count++
        obj.value.arr.push('baz')
        }

`### DOM Update Timing`

When you mutate reactive state, the DOM is updated automatically. However, it should be noted that the DOM updates are not applied synchronously. Instead, Vue buffers them until the "next tick" in the update cycle to ensure that each component updates only once no matter how many state changes you have made.

To wait for the DOM update to complete after a state change, you can use the nextTick() global API:

        import { nextTick } from 'vue'

        async function increment() {
        count.value++
        await nextTick()
        // Now the DOM is updated
        }

## reactive()

There is another way to declare reactive state, with the `reactive()` API. Unlike a ref which wraps the inner value in a special object, `reactive()` makes an object itself reactive:

        import { reactive } from 'vue'

        const state = reactive({ count: 0 })

Usage in template:

        <button @click="state.count++">
            {{ state.count }}
        </button>

`### Reactive Proxy vs. Original`

It is important to note that the returned value from `reactive()` is a Proxy of the original object, which is not equal to the original object:

        const raw = {}
        const proxy = reactive(raw)

        // proxy is NOT equal to the original.
        console.log(proxy === raw) // false

Only the proxy is reactive - mutating the original object will not trigger updates. Therefore, the best practice when working with Vue's reactivity system is to **exclusively use the proxied versions of your state**.

`### Limitations of reactive()`

The reactive() API has a few limitations:

- Limited value types: it only works for object types (objects, arrays, and collection types such as `Map` and `Set`). It cannot hold primitive types such as `string`, `number` or `boolean`.

- Cannot replace entire object:

        let state = reactive({ count: 0 })

        // the above reference ({ count: 0 }) is no longer being tracked
        // (reactivity connection is lost!)
        state = reactive({ count: 1 })

- Not destructure-friendly:

        const state = reactive({ count: 0 })

        // count is disconnected from state.count when destructured.
        let { count } = state
        // does not affect original state
        count++

        // the function receives a plain number and
        // won't be able to track changes to state.count
        // we have to pass the entire object in to retain reactivity
        callSomeFunction(state.count)

Due to these limitations, we recommend using `ref()` as the primary API for declaring reactive state.

## Additional Ref Unwrapping Details

`### As Reactive Object Property`

A ref is automatically unwrapped when accessed or mutated as a property of a reactive object. In other words, it behaves like a normal property:

        const count = ref(0)
        const state = reactive({
        count
        })

        console.log(state.count) // 0

        state.count = 1
        console.log(count.value) // 1
