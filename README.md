# Yandex SmartCaptcha for Vue3 projects

Adds [Yandex SmartCaptcha](https://cloud.yandex.ru/docs/smartcaptcha/) component into your Vue3 application

You need to [create](https://cloud.yandex.ru/docs/smartcaptcha/operations/create-captcha) Captcha and [get](https://cloud.yandex.ru/docs/smartcaptcha/operations/get-keys) keys. You need client site key in order to activate captcha component and it is the only required property 

> NOTE: this package does NOT provides verification of response - you still need to [implement](https://cloud.yandex.ru/docs/smartcaptcha/concepts/validation) it 

## Installation

Install it using npm

```sh
npm install vue3-smart-captcha

# Or using pnpm
pnpm add vue3-smart-captcha
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

| Property             | Default                     | Type                                                                                         |
|----------------------|-----------------------------|----------------------------------------------------------------------------------------------|
| **sitekey**          |  -                          | `string`                                                                                     |
| callback             | `undefined`                 | `(token: string) => void`                                                                    |
| hl                   | `window.navigator.language` | `'ru' | 'en' | 'be' | 'kk' | 'tt' | 'uk' | 'uz' | 'tr'`                                      |
| test                 | `false`                     | `boolean`                                                                                    |
| webview              | `false`                     | `boolean`                                                                                    |
| invisible            | `false`                     | `boolean`                                                                                    |
| shieldPosition       | `center-right`              | `'top-left' | 'center-left' | 'bottom-left' | 'top-right' | 'center-right' | 'bottom-right'` | 
| hideShield           | `false`                     | `boolean`                                                                                    |
| on-success           | `undefined`                 | `(token: string) => void`                                                                    |
| on-network-error     | `undefined`                 | `() => void`                                                                                 |
| on-challenge-visible | `undefined`                 | `() => void`                                                                                 |
| on-challenge-hidden  | `undefined`                 | `() => void`                                                                                 |
| on-token-expired     | `undefined`                 | `() => void`                                                                                 |

Basically it gets every parameter of `window.smartCaptcha` [object](https://cloud.yandex.ru/docs/smartcaptcha/concepts/widget-methods#methods) plus 5 callbacks for every [subscription](https://cloud.yandex.ru/docs/smartcaptcha/concepts/widget-methods#subscribe) events named as `on` + event name in camelCase ('success' => 'onSuccess', 'network-error' => 'onNetworkError', etc)

## License

Open-source under [MIT license](LICENSE)

## Testing

We are using [Vitest](https://vitest.dev/guide/)

> Help wanted to cover properties were passed to `window.smartCaptcha` object

```sh
pnpm test
```
