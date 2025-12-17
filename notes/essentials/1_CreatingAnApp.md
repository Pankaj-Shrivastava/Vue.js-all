# Creating a Vue Application

## The Application Instance

The createApp function:

        import { createApp } from 'vue'

        const app = createApp({
            /* root component options */
        })

## The Root Component

The object we are passing into createApp is in fact a component. Every app requires a "root component" that can contain other components as its children.

        import { createApp } from 'vue'
        // import the root component App from a single-file component.
        import App from './App.vue'

        const app = createApp(App)

## Mounting the App

An application instance won't render anything until its .mount() method is called. It expects a "container" argument, which can either be an actual DOM element or a selector string.

        app.mount('#app')

## App Configurations

The application instance exposes a .config object that allows us to configure a few app-level options, for example, defining an app-level error handler that captures errors from all descendant components:

        app.config.errorHandler = (err) => {
        /* handle error */
        }

The application instance also provides a few methods for registering app-scoped assets. For example, registering a component:

        app.component('TodoDeleteButton', TodoDeleteButton)

## Multiple Application Instances

You are not limited to a single application instance on the same page. The `createApp` API allows multiple Vue applications to co-exist on the same page, each with its own scope for configuration and global assets:

        const app1 = createApp({
        /* ... */
        })
        app1.mount('#container-1')

        const app2 = createApp({
        /* ... */
        })
        app2.mount('#container-2')

If you are using Vue to enhance server-rendered HTML and only need Vue to control specific parts of a large page, avoid mounting a single Vue application instance on the entire page. Instead, create multiple small application instances and mount them on the elements they are responsible for.
