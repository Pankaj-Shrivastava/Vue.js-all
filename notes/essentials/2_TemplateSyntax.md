# Template Syntax

## Text Interpolation​

The most basic form of data binding is text interpolation using the "Mustache" syntax (double curly braces):

        <span>Message: {{ msg }}</span>

## Raw HTML

The double mustaches interpret the data as plain text, not HTML. In order to output real HTML, you will need to use the v-html directive:

        <p>Using text interpolation: {{ rawHtml }}</p>
        <p>Using v-html directive: <span v-html="rawHtml"></span></p>

> [!Security Warning]
> Dynamically rendering arbitrary HTML on your website can be very dangerous because it can easily lead to XSS vulnerabilities. Only use v-html on trusted content and never on user-provided content.
