<template>
    <div>
        <b-modal ref="scanPopup" ok-only ok-title="Close" ok-variant="secondary" :title="this.title" @ok="stopScanner" @hide="hide">
            <div class="container">
                <div class="row mb-2">
                    <div class="col">
                        <select v-model="activeCameraId" class="form-control">
                            <option :value="camera.id" v-for="(camera, index) in this.cameras" :key="camera.id">#{{index+1}} - {{formatName(camera.name)}}</option>
                        </select>
                    </div>
                </div>
                <div class="row mb-2">
                    <div class="col">
                        <b-form-checkbox
                            v-model="mirror"
                            value="true"
                            unchecked-value="false"> Mirror image?
                        </b-form-checkbox>
                    </div>
                </div>
            </div>
            <div class="container">
                <div class="row">
                    <div class="col">
                        <video ref="previewArea" width="100%"/>
                    </div>
                </div>
            </div>
        </b-modal>
    </div>
</template>
<script>
    import Instascan from '@mathewparet/instascan';
    export default {
        install(Vue)
        {
            Vue.component('qr-reader', this);
        },
        props: {
            scanPeriod: {
                type: Number,
                default: 5,
                required: false,
            },
            title: {
                type: String,
                default: 'Read QR Code',
                required: false,
            }
        },
        data()
        {
            return {
                scanner: null,
                activeCameraId: null,
                cameras: [],
                scanData: null,
                mirror: false,
            };
        },
        watch: {
            activeCameraId: {
                handler()
                {
                    this.cameras.forEach(camera => {
                        if(camera.id == this.activeCameraId)
                            this.selectCamera(camera);
                    });
                },
                immediate: true,
            },
            mirror: {
                handler()
                {
                    if(this.scanner) this.scanner.mirror = this.mirror == 'true' ? true : false;
                },
                immediate: true,
            }
        },
        methods: {
            formatName(name)
            {
                return name || '(unknown)';
            },
            selectCamera(camera)
            {
                this.activeCameraId = camera.id;
                this.scanner.start(camera);
            },
            show()
            {
                this.initiate();
                this.$refs.scanPopup.show();
            },
            hide()
            {
                this.stopScanner();
            },
            stopScanner()
            {
                this.scanner.stop();
            },
            initiate()
            {
                let self = this;
                this.scanner = new Instascan.Scanner({ 
                    video: this.$refs.previewArea, 
                    scanPeriod: this.scanPeriod, 
                    mirror: this.mirror,
                    continuous: true,
                });
                this.scanner.addListener('scan', function(content, image) {
                    self.$emit('scan', content);
                    self.$refs.scanPopup.hide();
                });
                Instascan.Camera.getCameras()
                    .then(cameras => {
                        self.cameras = cameras;
                        if(cameras.length > 1) 
                            self.selectCamera(cameras[1]);
                        else if(cameras.length == 1)
                            self.selectCamera(cameras[0]);
                        else
                        {
                            self.$emit('cameraNotFound');
                            console.error('No camera found!');
                        }
                    })
                    .catch(e => {
                        self.$emit('error', e);
                    });
            }
        }
    }
</script>
