# Nuxt 3 universal example

## Installation

```sh
npm add pinia @pinia/nuxt cookie js-cookie
npm add pinia-plugin-persistedstate-2
```

## Usage

```ts
// plugins/persistedstate.ts
import { createPersistedStatePlugin } from 'pinia-plugin-persistedstate-2'
import Cookies from 'js-cookie'
import cookie from 'cookie'

export default function ({ $pinia, ssrContext }) {
  $pinia.use(
    createPersistedStatePlugin({
      // plugin options goes here
      storage: {
        getItem: (key) => {
          // See https://nuxtjs.org/guide/plugins/#using-process-flags
          if (process.server) {
            const parsedCookies = cookie.parse(ssrContext.req.headers.cookie)
            return parsedCookies[key]
          } else {
            return Cookies.get(key)
          }
        },
        // Please see https://github.com/js-cookie/js-cookie#json, on how to handle JSON.
        setItem: (key, value) =>
          Cookies.set(key, value, { expires: 365, secure: false }),
        removeItem: (key) => Cookies.remove(key),
      },
    }),
  )
}
```

```js
// nuxt.config.js
// https://v3.nuxtjs.org/docs/directory-structure/nuxt.config
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],
})
```
