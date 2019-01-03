# @mathewparet/vue-qr-reader

> A VueJS compoenent (Modal) that integrates with @mathewparet/instascan to read qr code.

## Installation

``` bash
npm install @mathewparet/vue-qr-reader
```

## Global Import
```js
import QrReader from '@mathewparet/vue-qr-reader';
Vue.use(QrReader);
```

## Import in local scope
```js
import QrReader from '@mathewparet/vue-qr-reader';
export default {
    components: {
        QrReader
    }
}
```

## Attributes

Name | Type | Required | Default | Description
--- | --- | --- | --- | ---
```scan-period``` | Number | No | 5 | The frame rate at which QR has to be captured
```title``` | String | No | ```'Read QR Code'``` | Title of the QR Reader Modal window

## Usage

```html
<template>
    <qr-reader title="Scan Code" ref="reader" @scan="scanned"/>
    <a class="btn btn-secondaru" @click="scan">Scan</a>
</template>
<script>
    export default {
        methods: {
            scanned(data)
            {
                console.log(data); // has the scanned raw data
                this.$refs.reader.hide(); // hide the reader so no more QRs are scanned
            },
            scan()
            {
                this.$refs.reader.show();
            }
        }
    }
</script>
```
## Events
```scan```

This event is called when a QR is read.

```cameraNotFound```

This event is called if no camera is detected.

```error```

This event is called for any error that occurs.