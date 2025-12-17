# Template Syntax

## Text Interpolation​

The most basic form of data binding is text interpolation using the "Mustache" syntax (double curly braces):

        <span>Message: {{ msg }}</span>

## Raw HTML

The double mustaches interpret the data as plain text, not HTML. In order to output real HTML, you will need to use the v-html directive:

        <p>Using text interpolation: {{ rawHtml }}</p>
        <p>Using v-html directive: <span v-html="rawHtml"></span></p>

> [!Warning]
> Dynamically rendering arbitrary HTML on your website can be very dangerous because it can easily lead to XSS vulnerabilities. Only use v-html on trusted content and never on user-provided content.

## Attribute Bindings​

Mustaches cannot be used inside HTML attributes. Instead, use a v-bind directive:

        <div v-bind:id="dynamicId"></div>

The `v-bind` directive instructs Vue to keep the element's id attribute in sync with the component's `dynamicId` property. If the bound value is `null` or `undefined`, then the attribute will be removed from the rendered element.

### Shorthand​

Because v-bind is so commonly used, it has a dedicated shorthand syntax:

        <div :id="dynamicId"></div>

### Same-name Shorthand​

- Only supported in 3.4+

If the attribute has the same name as the variable name of the JavaScript value being bound, the syntax can be further shortened to omit the attribute value:

        <!-- same as :id="id" -->
        <div :id></div>

        <!-- this also works -->
        <div v-bind:id></div>

This is similar to the property shorthand syntax when declaring objects in JavaScript. Note this is a feature that is only available in Vue 3.4 and above.

### Boolean Attributes​

Boolean attributes are attributes that can indicate true / false values by their presence on an element. For example, `disabled` is one of the most commonly used boolean attributes.

        <button :disabled="isButtonDisabled">Button</button>

### Dynamically Binding Multiple Attributes​

If you have a JavaScript object representing multiple attributes that looks like this:

        const objectOfAttrs = {
        id: 'container',
        class: 'wrapper',
        style: 'background-color:green'
        }

You can bind them to a single element by using v-bind without an argument:

        <div v-bind="objectOfAttrs"></div>

## Using Javascript Expressions

        {{ number + 1 }}

        {{ ok ? 'YES' : 'NO' }}

        {{ message.split('').reverse().join('') }}

        <div :id="`list-${id}`"></div>

### Calling Functions

It is possible to call a component-exposed method inside a binding expression:

        <time :title="toTitleDate(date)" :datetime="date">
        {{ formatDate(date) }}
        </time>

> [!TIP]
> Functions called inside binding expressions will be called every time the component updates, so they should not have any side effects, such as changing data or triggering asynchronous operations.

## Directives

Directives are special attributes with the v- prefix. Vue provides a number of built-in directives, including `v-html` and `v-bind`.

        <p v-if="seen">Now you see me</p>

Here, the v-if directive would remove or insert the <p> element based on the truthiness of the value of the expression `seen`.

### Arguments

        <a v-bind:href="url"> ... </a>

        <!-- shorthand -->
        <a :href="url"> ... </a>

Here, `href` is the argument, which tells the `v-bind` directive to bind the element's `href` attribute to the value of the expression `url`.

Another example is the `v-on` directive, which listens to DOM events:

        <a v-on:click="doSomething"> ... </a>

        <!-- shorthand -->
        <a @click="doSomething"> ... </a>

Here, the argument is the event name to listen to: `click`. `v-on` has a corresponding shorthand, namely the `@` character.

### Dynamic Arguments​

It is also possible to use a JavaScript expression in a directive argument by wrapping it with square brackets:

        <!--
        Note that there are some constraints to the argument expression,
        as explained in the "Dynamic Argument Value Constraints" and "Dynamic Argument Syntax Constraints" sections below.
        -->
        <a v-bind:[attributeName]="url"> ... </a>

        <!-- shorthand -->
        <a :[attributeName]="url"> ... </a>

Similarly, you can use dynamic arguments to bind a handler to a dynamic event name:

        <a v-on:[eventName]="doSomething"> ... </a>

        <!-- shorthand -->
        <a @[eventName]="doSomething"> ... </a>

In this example, when `eventName's` value is `"focus"`, `v-on:[eventName]` will be equivalent to `v-on:focus`.

If you need to pass a complex dynamic argument, it's probably better to use a computed property.

### Modifiers​

Modifiers are special postfixes denoted by a dot, which indicate that a directive should be bound in some special way. For example, the `.prevent` modifier tells the `v-on` directive to call `event.preventDefault()` on the triggered event:

        <form @submit.prevent="onSubmit">...</form>

