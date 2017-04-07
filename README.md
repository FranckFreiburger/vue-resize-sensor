# vue-resize-sensor
detect container resizing. 
* event based
* no window.onresize
* no interval/timeout detection
* no CSS modifications
* no Javascript-Framework dependency

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
Any browser, IE7+
