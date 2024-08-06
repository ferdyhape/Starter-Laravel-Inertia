# Basic Setup Laravel Inertia

### Version Information

-   Laravel 10
-   Node 20.14.0

### Notes

-   Before it starts, make sure you have installed the Laravel and Node in your system. If you don't have it, you can install it by following the instructions in the [Laravel](https://laravel.com/docs/10.x/installation) and [Node](https://nodejs.org/en/download/) documentation.
-   Open split terminal in this project for running node server and laravel server

### Steps

1.  Create a new Laravel project

    ```sh
    composer create-project laravel/laravel example-app
    ```

2.  Server Side Setup Inertia

    -   Install Inertia
        ```sh
        composer require inertiajs/inertia-laravel
        ```
    -   Create [app.blade.php](/resources/views/app.blade.php) in resources/views/, and add the following code
        ```html
        <!DOCTYPE html>
        <html>
            <head>
                <meta charset="utf-8" />
                <meta
                    name="viewport"
                    content="width=device-width, initial-scale=1.0, maximum-scale=1.0"
                />
                @vite('resources/js/app.js') @inertiaHead
            </head>
            <body>
                @inertia
            </body>
        </html>
        ```
    -   Publishing inertia middleware
        ```sh
        php artisan inertia:middleware
        ```
    -   Append HandleInertiaRequests middleware to the web middleware group, in Laravel 10, it is located in [Kernel.php](/app/Http/Kernel.php)

        ```php
        // app/Http/Kernel.php

        protected $middlewareGroups = [
            'web' => [
                // ...
                \App\Http\Middleware\HandleInertiaRequests::class,
            ],
        ];
        ```

3.  Client Side Inertia Setup

    -   Install Inertia
        ```sh
        npm install @inertiajs/inertia @inertiajs/vue3
        ```
    -   modify [app.js](/resources/js/app.js) in resources/js/

        ```js
        import { createApp, h } from "vue";
        import { createInertiaApp } from "@inertiajs/vue3";

        createInertiaApp({
            resolve: (name) => {
                const pages = import.meta.glob("./Pages/**/*.vue", {
                    eager: true,
                });
                return pages[`./Pages/${name}.vue`];
            },
            setup({ el, App, props, plugin }) {
                createApp({ render: () => h(App, props) })
                    .use(plugin)
                    .mount(el);
            },
        });
        ```

    -   Install Vue Plugin for Vite and vue

        ```sh
        npm install @vitejs/plugin-vue vue
        ```

    -   Modify [vite.config.js](/vite.config.js)

        ```js
        import { defineConfig } from "vite";
        import laravel from "laravel-vite-plugin";
        import vue from "@vitejs/plugin-vue";

        export default defineConfig({
            plugins: [
                vue(),
                laravel({
                    input: ["resources/css/app.css", "resources/js/app.js"],
                    refresh: true,
                }),
            ],
        });
        ```

4.  Create Pages in resources/js/Pages/

    -   [Home.vue](/resources/js/Pages/Home.vue)

        ```js
        <template>
            <div>
                <h1>My Inertia Index</h1>
                <Link href="/about">About</Link>
            </div>
        </template>

        <script setup>
        import { Link } from "@inertiajs/vue3";
        </script>
        ```

    -   [About.vue](/resources/js/Pages/About.vue)

        ```js
        <template>
            <div>
                <h1>About</h1>
                <Link href="/">Home</Link>
            </div>
        </template>

        <script setup>
        import { Link } from "@inertiajs/vue3";
        </script>
        ```

5.  Update [routes/web.php](/routes/web.php)

    ```php
    use Inertia\Inertia;

    Route::get('/', function () {
        return Inertia::render('Index');
    });

    Route::get('/about', function () {
        return Inertia::render('About');
    });
    ```

6.  Run node server

    ```sh
    npm run dev
    ```

7.  Run Laravel Server

    ```sh
    php artisan serve
    ```
