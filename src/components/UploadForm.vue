<template>
    <div class="dragndrop"
        @dragover.prevent="enter"
        @dragenter.prevent="enter"
        @dragleave.prevent="leave"
        @dragend.prevent="leave"
        @drop.prevent="drop"
        v-bind:class="{'dragndrop--dragged' : isDraggedOver}"
    >
    {{ isDraggedOver }}
        <input type="file" name="files[]" id="file" class="dragndrop__input" multiple v-on:change="select" ref="input">
        <label for="file" class="dragndrop__header" v-bind:class="{ 'dragndrop__compact' :  files.length >= 1 }">
            <strong>Drag files here</strong> or click to select files
        </label>
        <uploads :files="files"></uploads>
    </div>
</template>

<script>
import Uploads from './Uploads'
    export default {
        data () {
            return {
                files: [],
                isDraggedOver: false
            }
        },
        components: {
            Uploads
        },
        methods: {
            enter () {
                this.isDraggedOver = true
            },
            leave () {
                this.isDraggedOver = false
            },
            drop (e) {
                this.leave()
                this.addFiles(e.dataTransfer.files)
            },
            select (e) {
                this.addFiles(this.$refs.input.files)
            },
            addFiles (files) {
                for(let i = 0, file; i < files.length; i++) {
                    file = files[i]

                    this.storeMeta(file).then(fileObject => {
                        this.upload(fileObject)
                    }, fileObject => {
                        fileObject.failed = true
                    })
                }
            },
            upload (fileObject) {
                let form = new FormData()
                form.append('file', fileObject.file)
                form.append('id', fileObject.id)

                this.$http.post('http', form, {
                    before: xhr => {
                        fileObject.xhr = xhr
                    },
                    progress: e => {
                        console.log(e.loaded)
                    }
                }). then(response => {
                    console.log('finished')
                }, () => {

                })
            },
            storeMeta (file) {
                let fileObject = this.generateFileObject(file)
                
                return new Promise((resolve, reject) => {
                    this.$http.post('http', {
                        name: file.name
                    }).then(response => {
                        fileObject.id = response.body.data.id
                        resolve(fileObject)
                    }, () => {
                        reject(fileObject)
                    })
                })
            },
            generateFileObject (file) {
                let fileObjectIndex = this.files.push({
                    id: null,
                    file: file,
                    progress: 0,
                    failed: false,
                    loadedBytes: 0,
                    totalBytes: 0,
                    timeStarted: (new Date).getTime(),
                    secondsRemaining: 0,
                    finished: false,
                    cancelled: false,
                    xhr: null
                }) - 1

                return this.files[fileObjectIndex]
            }
        }
    }
</script>

<style scoped>
    .dragndrop {
        --dragndrop-min-height: 200px;
        width: 100%;
        min-height: var(--dragndrop-min-height);
        background-color: #f8f8f8;
        position: relative;
        border: 3px dashed rgb(0, 0, 0, .2);
    }
    .dragndrop--dragged {
        border-color: #333;
    }
    .dragndrop__input {
        display: none;
    }
    .dragndrop__header {
        display: block;
        font-size: 1.1em;
        color: #555;
        vertical-align: middle;
        text-align: center;
        margin: calc(var(--dragndrop-min-height) / 2) 20px;
    }
    .dragndrop__header:hover {
        text-decoration: underline;
        cursor: pointer;
    }
    .dragndrop__header--compact {
        margin: calc(var(--dragndrop-min-height) / 4) 20px;
    }
</style>
