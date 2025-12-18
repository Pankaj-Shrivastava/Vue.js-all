# Computed Properties

## Basic Example

For complex logic that includes reactive data, it is recommended to use a **computed property**. 

        export default {
            data() {
                return {
                    author: {
                        name: 'John Doe',
                        books: [
                        'Vue 2 - Advanced Guide',
                        'Vue 3 - Basic Guide',
                        'Vue 4 - The Mystery'
                        ]
                    }
                }
            },
            computed: {
                // a computed getter
                publishedBooksMessage() {
                    // `this` points to the component instance
                    return this.author.books.length > 0 ? 'Yes' : 'No'
                }
            }
        }

Template:

        <p>Has published books:</p>
        <span>{{ publishedBooksMessage }}</span>

You can data-bind to computed properties in templates just like a normal property. Vue is aware that `this.publishedBooksMessage` depends on `this.author.books`, so it will update any bindings that depend on this.publishedBooksMessage when `this.author.books` changes.

## Computed Caching vs. Methods

Instead of a computed property, we can define the same function as a method. 

        <p>{{ calculateBooksMessage() }}</p>

        // in component
        methods: {
            calculateBooksMessage() {
                return this.author.books.length > 0 ? 'Yes' : 'No'
            }
        }

For the end result, the two approaches are indeed exactly the same. However, the difference is that **computed properties are cached based on their reactive dependencies**. A computed property will only re-evaluate when some of its reactive dependencies have changed. This means as long as `author.books` has not changed, multiple access to `publishedBooksMessage` will immediately return the previously computed result without having to run the getter function again.

In comparison, a method invocation will always run the function whenever a re-render happens.

## Writable Computed

Computed properties are by default getter-only. If you attempt to assign a new value to a computed property, you will receive a runtime warning. In the rare cases where you need a "writable" computed property, you can create one by providing both a getter and a setter:

        export default {
            data() {
                return {
                    firstname: 'John',
                    lastname: 'Doe'
                }
            },
            computed: {
                fullName: {
                    // getter
                    get() {
                        return this.firstName + ' ' + this.lastName
                    },
                    // setter
                    set(newValue) {
                        // Note: we are using destructuring assignment syntax here.
                        [this.firstName, this.lastName] = newValue.split(' ')
                    }
                }
            }
        }

Now when you run `this.fullName = 'John Doe'`, the setter will be invoked and `this.firstName` and `this.lastName` will be updated accordingly.

## Getting the Previous Value

- Only supported in 3.4+

In case you need it, you can get the previous value returned by the computed property accessing the second argument of the getter:

        export default {
            data() {
                return {
                    count: 2
                }
            },
            computed: {
                // This computed will return the value of count when it's less or equal to 3.
                // When count is >=4, the last value that fulfilled our condition will be returned
                // instead until count is less or equal to 3
                alwaysSmall(_, previous) {
                    if (this.count <= 3) {
                        return this.count
                    }

                    return previous
                }
            }
        }

In case you're using a writable computed:

        export default {
            data() {
                return {
                    count: 2
                }
            },
            computed: {
                alwaysSmall: {
                    get(_, previous) {
                        if (this.count <= 3) {
                            return this.count
                        }

                        return previous;
                    },
                    set(newValue) {
                        this.count = newValue * 2
                    }
                }
            }
        }

## Best Practices

### Getters should be side-effect free​

It is important to remember that computed getter functions should only perform pure computation and be free of side effects. For example, **don't mutate other state, make async requests, or mutate the DOM inside a computed getter!** Think of a computed property as declaratively describing how to derive a value based on other values - its only responsibility should be computing and returning that value.

### Avoid mutating computed value​

The returned value from a computed property is derived state. Think of it as a temporary snapshot - every time the source state changes, a new snapshot is created. It does not make sense to mutate a snapshot, so a computed return value should be treated as read-only and never be mutated - instead, update the source state it depends on to trigger new computations.
