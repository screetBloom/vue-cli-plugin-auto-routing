# vue-cli-plugin-auto-routing

[![vue-cli-plugin-auto-routing Dev Token](https://badge.devtoken.rocks/vue-cli-plugin-auto-routing)](https://devtoken.rocks/package/vue-cli-plugin-auto-routing)

Vue CLI plugin that provides auto routing feature.

## Overview

Ensure you are in a project generated by Vue CLI v3. You install this plugin by running the following command:

```bash
# If you did not install router plugin yet
$ vue add router

# Install vue-cli-plugin-auto-routing
$ vue add auto-routing
```

After adding the plugin, the file structure will be the below.

```
src/
├── pages/
├── layouts/
└── router.js
```

### Pages

`.vue` files under the `pages/` directory will be automatically resolved to generate routing. Vue Router `routes` options will be configured with the file structure under the `pages/`. The generated routing is same with [Nuxt's routing](https://nuxtjs.org/guide/routing).

Note that directories and files started and ended with `__` (two underscores, e.g. `__foo__.vue`) will be ignored.

For example, when you have the following page components:

```
pages/
├── __partial__.vue
├── index.vue
├── users.vue
└── users/
    └── _id.vue
```

It is resolved to the below routes. Note that `_` prefixed components generate dynamic routing.

```js
export default [
  {
    name: 'index',
    path: '/',
    component: () => import('@/pages/index.vue')
  },
  {
    name: 'users',
    path: '/users',
    component: () => import('@/pages/users.vue'),
    children: [
      {
        name: 'users-id',
        path: ':id?',
        component: () => import('@/pages/users/_id.vue')
      }
    ]
  }
]
```

If you want to make route param required, create a directory for the param and add `index.vue` in the directory. In the above example, if you replace `users/_id.vue` with `users/_id/index.vue`, `:id` param will be required.

#### `<route-meta>` custom block

If a page component has `<route-meta>` custom block, the content json will be used as [route meta field](https://router.vuejs.org/guide/advanced/meta.html).

For example, if `index.vue` has the following `<route-meta>` block:

```vue
<route-meta>
{
  "requiresAuth": true
}
</route-meta>

<template>
  <h1>Hello</h1>
</template>
```

The generated route config is like the following:

```js
module.exports = [
  {
    name: 'index',
    path: '/',
    component: () => import('@/pages/index.vue'),
    meta: {
      requiresAuth: true
    }
  }
]
```

### Layouts

Components under the `layouts/` directory will be used as shared layout component in the application. You can choose a layout by specifying `layout` component option in a page component. The default value of `layout` is `'default'`. That means when you omit `layout` options, `layouts/default.vue` will be choosed as the layout component. This is the same concept with [Nuxt's layouts](https://nuxtjs.org/guide/views#layouts).

For example, when you have `layouts/foo.vue` and `pages/index.vue`:

```vue
<!-- layouts/foo.vue -->
<template>
  <div>
    <h1>Foo Layout</h1>
    <router-view />
  </div>
</template>
```

```vue
<!-- pages/index.vue -->
<template>
  <p>index.vue</p>
</template>

<script>
export default {
  // You can specify layout component name here (default value is 'default')
  layout: 'foo'
}
</script>
```

The following html will be rendered:

```html
<div>
  <h1>Foo Layout</h1>
  <p>index.vue</p>
</div>
```

## Related Projects

- [vue-router-layout](https://github.com/ktsn/vue-router-layout): Lightweight layout resolver for Vue Router.
- [vue-auto-routing](https://github.com/ktsn/vue-auto-routing): Generate Vue Router routing automatically.
- [vue-route-generator](https://github.com/ktsn/vue-route-generator): Low-level utility generating routing (used by vue-auto-routing under the hood).

## License

MIT
