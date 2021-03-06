<!--
Template tags:
extend-chrome
notify
@extend-chrome
https://imgur.com/0lQYbgT.png
-->

<p align="center">
  <a href="https://github.com/extend-chrome/notify" rel="noopener">
  <img width=200px height=200px src="https://i.imgur.com/HCX1Dpk.png" alt="@extend-chrome/notify logo"></a>
</p>

<h3 align="center">@extend-chrome/notify</h3>

<div align="center">

[![npm (scoped)](https://img.shields.io/npm/v/@extend-chrome/notify.svg)](https://www.npmjs.com/package/@extend-chrome/notify)
[![GitHub last commit](https://img.shields.io/github/last-commit/extend-chrome/notify.svg)](https://github.com/extend-chrome/notify)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](/LICENSE)
[![TypeScript Declarations Included](https://img.shields.io/badge/types-TypeScript-informational)](#typescript)

</div>

<div align="center">

[![Chrome Extension Tutorials on YouTube](https://img.shields.io/badge/Chrome%20Extension%20Tutorials-YouTube-c4302b.svg)](https://www.youtube.com/channel/UCVj3dGw75v8aHFYD6CL1tFg)
[![ko-fi](https://img.shields.io/badge/Buy%20us%20a%20tea-ko--fi-29ABE0)](https://ko-fi.com/jacksteam)

</div>

---

This is a simpler API for [`chrome.notifications`](https://developer.chrome.com/extensions/notifications) to use in Chrome extensions.

Add the [`notifications` permission](#permissions) and [create a notification](#usage) with as little as a string. `@extend-chrome/notify` will [do the rest](#manifest)! ✨

```javascript
notify('This is too easy')
```

## Table of Contents

- [Getting Started](#getting_started)
- [Usage](#usage)
- [Features](#features)
- [API](#api)

## Getting started <a name = "getting_started"></a>

You will need to use a bundler like [Rollup](https://rollupjs.org/guide/en/) or Webpack to include this library in the build of Chrome extension.

See [`rollup-plugin-chrome-extension`](https://github.com/extend-chrome/rollup-plugin-chrome-extension) for an easy way use Rollup to build your Chrome extension!

### Installation

```sh
$ npm i @extend-chrome/notify
```

## Usage <a name = "usage"></a>

```javascript
import { notify } from '@extend-chrome/notify'

notify('The most simple notification').then((id) => {
  console.log('notification id', id)
})

notify
  .create({
    message: 'You have been notified.',
  })
  .then((id) => {
    console.log('notification id', id)
  })
```

The function `notify.create` takes any of the [official notification options](https://developer.chrome.com/extensions/notifications#type-NotificationOptions) for `chrome.notifications.create`, without trying to type `"notifications"` every time.

### Permissions <a name = "permissions"></a>

The `"notifications"` permission must be included in `manifest.json`.

```json
// manifest.json
{
  "permissions": ["notifications"]
}
```

## Features <a name = "features"></a>

### TypeScript Definitions <a name = "typescript"></a>

TypeScript definitions are included, so no need to install an additional `@types` library!

### Gets Name and Icon from `manifest.json` <a name = "manifest"></a>

This library will use `chrome.runtime.getManifest()` to include the [name](https://developer.chrome.com/extensions/manifest/name#name) and [icon](https://developer.chrome.com/extensions/manifest/icons) of your extension in your notifications!

## API <a name = "api"></a>

### `notify(message: string)`

Returns: `Promise<string>`

Create a simple notification with an icon and the name of the Chrome extension, if they are supplied in `manifest.json`.

Returns a promise which resolves to the notification id, which you can use in the `notify.onClick` and `notify.onButtonClick` events.

```javascript
const myId = await notify('This is my notification')

notify.onClicked.addListener((clickedId) => {
  if (myId === clickedId) {
    console.log('My notification was clicked.')
  }
})
```

### `notify.create(options: NotificationOptions)`

Returns: `Promise<string>`

Create a [basic notification](https://developer.chrome.com/extensions/notifications#type-TemplateType) by default using as little as `options.message`, or any of the other properties in [NotificationOptions](https://developer.chrome.com/extensions/notifications#type-NotificationOptions).

Returns a promise which resolves to the notification id, which you can use in [notification events](#api-events).

```javascript
const myId = await notify.create({
  message: 'This is my notification',
})

notify.onClicked.addListener((clickedId) => {
  if (myId === clickedId) {
    console.log('My notification was clicked.')
  }
})
```

### Other methods and events

All the other methods and events from [`chrome.notifications`](https://developer.chrome.com/extensions/notifications) are promisified using [`chrome-promise`](https://github.com/tfoxy/chrome-promise) and assigned to `notify`, so you can use `notify` as if it is `chrome.notifications` with promises. These include the following:

#### Methods <a name = "api-methods"></a>

Methods return promises but are otherwise the same as the Chrome API.

```javascript
notify
  .update('my-notification', updateOptions)
  .then((wasUpdated) => {
    if (wasUpdated) {
      console.log('my notification was updated')
    }
  })
```

- [`update(id) => Promise<wasUpdated: boolean>`](https://developer.chrome.com/extensions/notifications#method-update)
- [`clear(id) => Promise<wasCleared: boolean>`](https://developer.chrome.com/extensions/notifications#method-clear)
- [`getAll() => Promise<notificationIds: string[]>`](https://developer.chrome.com/extensions/notifications#method-getAll)
- [`getPermissionsLevel() => Promise<'granted'|'denied'>`](https://developer.chrome.com/extensions/notifications#method-getPermissionLevel)

#### Events <a name = "api-events"></a>

Events are exacly the same as the Chrome API. Register a listener by calling `addListener` on an event:

```javascript
notify.onClosed.addListener((id) => {
  console.log('This notification was closed', id)
})
```

- [`onClosed`](https://developer.chrome.com/extensions/notifications#event-onClosed)
- [`onClicked`](https://developer.chrome.com/extensions/notifications#event-onClicked)
- [`onButtonClicked`](https://developer.chrome.com/extensions/notifications#event-onButtonClicked)
- [`onPermissionLevelChanged`](https://developer.chrome.com/extensions/notifications#event-onPermissionLevelChanged)
- [`onShowSettings`](https://developer.chrome.com/extensions/notifications#event-onShowSettings)
