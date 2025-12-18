# Class and Style Bindings

A common need for data binding is manipulating an element's class list and inline styles. Since `class` and `style` are both attributes, we can use `v-bind` to assign them a string value dynamically, much like with other attributes. However, trying to generate those values using string concatenation can be annoying and error-prone. For this reason, Vue provides special enhancements when `v-bind` is used with `class` and `style`. In addition to strings, the expressions can also evaluate to objects or arrays.

## Binding HTML Classes

We can pass an object to `:class` (short for `v-bind:class`) to dynamically toggle classes:

        <div :class="{ active: isActive }"></div>

The `:class` directive can also co-exist with the plain `class` attribute. So given the following state:

        data() {
            return {
                isActive: true,
                hasError: false
            }
        }

And the following template:

        <div
            class="static"
            :class="{ active: isActive, 'text-danger': hasError }"
        ></div>

It will render:

        <div class="static active"></div>

### Binding to Arrays​

We can bind `:class` to an array to apply a list of classes:

        data() {
            return {
                activeClass: 'active',
                errorClass: 'text-danger'
            }
        }

Template:

        <div :class="[activeClass, errorClass]"></div>

Which will render:

        <div class="active text-danger"></div>

### With Components

If your component has multiple root elements, you would need to define which element will receive this class. You can do this using the `$attrs` component property:

        <!-- MyComponent template using $attrs -->
        <p :class="$attrs.class">Hi!</p>
        <span>This is a child component</span>

Template:

        <MyComponent class="baz" />

Will render:

        <p class="baz">Hi!</p>
        <span>This is a child component</span>

## Binding Inline Styles

### Binding to Objects

It is often a good idea to bind to a style object directly so that the template is cleaner:

        data() {
            return {
                styleObject: {
                    color: 'red',
                    fontSize: '13px'
                }
            }
        }

Template:

        <div :style="styleObject"></div>

### Binding Inline Styles to Arrays​

We can bind `:style` to an array of multiple style objects. These objects will be merged and applied to the same element:

        <div :style="[baseStyles, overridingStyles]"></div>

### Multiple Values​

You can provide an array of multiple (prefixed) values to a style property, for example:

        <div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>

This will only render the last value in the array which the browser supports. In this example, it will render `display: flex` for browsers that support the unprefixed version of flexbox.
