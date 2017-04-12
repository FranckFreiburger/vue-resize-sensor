# vue-resize-sensor
detect container resizing. 
* event based
* no window.onresize
* no interval/timeout detection
* no CSS modifications
* no Javascript-Framework dependency

Note for old browsers (IE9-): timeout-based polling is done to check resize-sensor element visibility, once visible, polling is stopped.

## Example
```
<template>
  <div>
    <resize-sensor @resize="resize"></resize-sensor>
  </div>
</template>

```

## API

### events

#### @resize <sup>Object<sup>
A size object `{ width, height }`


## Browser support
Same browser support as [Vue.js 2](https://github.com/vuejs/vue/blob/dev/README.md)

