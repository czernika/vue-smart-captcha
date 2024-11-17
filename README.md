# Yandex SmartCaptcha for Vue3 projects

Adds [Yandex SmartCaptcha](https://cloud.yandex.ru/docs/smartcaptcha/) component into your Vue3 application

You need to [create](https://cloud.yandex.ru/docs/smartcaptcha/operations/create-captcha) Captcha and [get](https://cloud.yandex.ru/docs/smartcaptcha/operations/get-keys) keys. You need client site key in order to activate captcha component and it is the only required property 

> NOTE: this package does NOT provides verification of response - you still need to [implement](https://cloud.yandex.ru/docs/smartcaptcha/concepts/validation) it 

## Installation

Install it

```sh
npm install vue3-smart-captcha@0.5.0
```

## Usage

No need to add widget script in the head section of your site

### As plugin

```js
// main.js
import { createApp } from 'vue'
import { SmartCaptchaPlugin } from 'vue3-smart-captcha'

const app = createApp({})

app.use(SmartCaptchaPlugin)
```

### As component

```vue
<template>
    <SmartCaptcha :sitekey="sitekey" />
</template>

<script setup>
import { SmartCaptcha } from 'vue3-smart-captcha'

const sitekey = 'client_site_key' // import.meta.env.VITE_YANDEX_SMART_CAPTCHA_KEY
</script>
```

You may add `style="height: 100px"` in order to [prevent](https://cloud.yandex.ru/docs/smartcaptcha/operations/advanced-method) layout jump

```vue
<template>
    <SmartCaptcha style="height: 100px" :sitekey="sitekey" />
</template>
```

## Options

Only `sitekey` is required

| Property             | Default                     | Description                                                                | Type                                                                                         |
|----------------------|-----------------------------|----------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| **sitekey**          |  -                          | Public site key (find it at Yandex console panel)                          | `string`                                                                                     |
| loadWidget           | `true`                      | Load widget script or not (if not you should provide source script itself) | `boolean`                                                                                    |
| timeout              | `2000`                      | How much time will component looking for smartCaptcha object to initialize | `number`                                                                                     |
| callback             | `undefined`                 | [Render property (1)](https://cloud.yandex.ru/docs/smartcaptcha/concepts/widget-methods#render)                                                                          | `(token: string) => void`                                                                    |
| hl                   | `window.navigator.language` | See [1]                                                                          | `'ru', 'en', 'be', 'kk', 'tt', 'uk', 'uz', 'tr'`                                             |
| test                 | `false`                     | See [1]                                                                           | `boolean`                                                                                    |
| webview              | `false`                     | See [1]                                                                           | `boolean`                                                                                    |
| invisible            | `false`                     | See [1]                                                                           | `boolean`                                                                                    |
| shieldPosition       | `center-right`              | See [1]                                                                           | `'top-left', 'center-left', 'bottom-left', 'top-right', 'center-right', 'bottom-right'`      | 
| hideShield           | `false`                     | See [1]                                                                           | `boolean`                                                                                    |
| on-success           | `undefined`                 | [Subscription event (2)](https://cloud.yandex.ru/docs/smartcaptcha/concepts/widget-methods#subscribe)                                                         | `(token: string) => void`                                                                    |
| on-network-error     | `undefined`                 | See [2]                                                          | `() => void`                                                                                 |
| on-challenge-visible | `undefined`                 | See [2]                                                          | `() => void`                                                                                 |
| on-challenge-hidden  | `undefined`                 | See [2]                                                          | `() => void`                                                                                 |
| on-token-expired     | `undefined`                 | See [2]                                                          | `() => void`                                                                                 |

Basically it gets every parameter of `window.smartCaptcha` [object](https://cloud.yandex.ru/docs/smartcaptcha/concepts/widget-methods#methods) plus 5 callbacks for every [subscription](https://cloud.yandex.ru/docs/smartcaptcha/concepts/widget-methods#subscribe) events named as `on` + event name in camelCase ('success' => 'onSuccess', 'network-error' => 'onNetworkError', etc)

### Do not load widget

You may add script tag `<script src="https://smartcaptcha.yandexcloud.net/captcha.js?render=onload" defer></script>` yourself or using Nuxt config like

```js
export default defineNuxtConfig({
  app: {
    head: {
      script: [
        {
          src: 'https://smartcaptcha.yandexcloud.net/captcha.js?render=onload',
          defer: true,
        }
      ]
    }
  }
})
```

This way you don't need to render widget script itself. Just set `:load-widget="false"` to disable script loading

```vue
<template>
    <SmartCaptcha sitekey="sitekey" :load-widget="false" />
</template>
```

You can specify amount of time in `timeout` how much script will try to resolve `window.smartCaptcha` object before give up

## Invisible captcha

Pass invisible property

```vue
<template>
    <SmartCaptcha sitekey="sitekey" invisible />
</template>
```

Of course you need to handle validation yourself. You can use helper methods provided by this package or use global `window.smartCaptcha` object

## Helper methods

`window.smartCaptcha` object should be available globally. However package provides 4 helper methods with the same functionality

```js
import { 
    execute, // execute captcha
    destroy, // destroy it
    reset, // reset token
    getResponse, // receive token
} from 'vue3-smart-captcha'
```

## Events

When captcha initialized you can subscribe to `initialized` emitted event. Can be useful with invisible or multiple captchas

### Invisible captcha example using callback

```vue
<template>
    <SmartCaptcha
        :sitekey="siteKey"
        invisible
        :callback="send"
        @initialized="setWidgetId"
    />

    <button type="button" @click="submit">
        Submit
    </button>
</template>

<script setup>
import { SmartCaptcha, execute } from 'vue3-smart-captcha'

const siteKey = import.meta.env.VITE_SMARTCAPTCHA_SITE_KEY

const widgetID = ref()

const setWidgetId = (id) => {
    widgetID.value = id
}

const submit = () => {
    execute(widgetID.value)
}

const validateCaptcha = async (tkn) => {
    // send request to validate token
}

const send = async (tkn) => {
    // Captcha token is available
    console.log(tkn)
    
    // Handle validation response from backend
    // Prevent further submitting if response has error or token invalid/expired
    await validateCaptcha(token)

    // Send request to backend with form data if validation passed
    // ...
}
</script>
```

## License

Open-source under [MIT license](LICENSE)

## Testing

We are using [Vitest](https://vitest.dev/guide/)

> Help wanted to cover properties were passed to `window.smartCaptcha` object

## TODO

- [ ] - move to composable in v1
