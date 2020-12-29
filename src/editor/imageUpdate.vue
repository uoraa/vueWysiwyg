<template>
    <div class="modal__wrap">
        <img :src="thumb" class="loaded_target" />
        <span class="loaded_controls">
            <span class="closeit" @click="cancelUpdateImage">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-x" viewBox="0 0 16 16"><path fill-rule="evenodd" d="M4.646 4.646a.5.5 0 0 1 .708 0L8 7.293l2.646-2.647a.5.5 0 0 1 .708.708L8.707 8l2.647 2.646a.5.5 0 0 1-.708.708L8 8.707l-2.646 2.647a.5.5 0 0 1-.708-.708L7.293 8 4.646 5.354a.5.5 0 0 1 0-.708z"/></svg></span>
            <strong>
                Image Properties
            </strong>
            <br />
            <br />
            <div class="row">
                <div class="column">
                    <Dropzone
                        :options="dropzoneOptions"
                        :id="_uid+'vwdropzone'"
                        ref="dropzone"
                        :include-styling="false"
                        :useCustomSlot="true"
                        @vdropzone-success="fileUploaded"
                        @vdropzone-file-added="fileAdded"
                        class="dropzone_replace"
                    >
                        <div class="dropzone-custom-content">
                            <span v-html="dropzoneContent"></span>
                        </div>
                    </Dropzone>
                </div>
                <div class="column">
                    Alignment:
                    <br />
                    <br />
                    <select v-model="sendBack.alignment" style="width: 100%;">
                    <option disabled value="">Select</option>
                        <option>left</option>
                        <option>right</option>
                    </select>
                    <span v-if="sendBack.alignment != ''" @click="sendBack.alignment = ''" class="clearalignment">clear</span>
                </div>
                <div class="column">
                    Size:
                    <br />
                    <br />
                    width: <input type="number" v-model="sendBack.width" style="width: 100%;" /><br />
                </div>
                <div class="column colright">
                    <button @click="updateImage" class="upim_btn">Update</button>
                </div>
            </div>
        </span>
    </div>
</template>

<script>
import Dropzone from 'vue2-dropzone';

const UPLOAD_ICON = '<svg fill="#666" width="26" height="24" viewBox="0 0 2048 1792" xmlns="http://www.w3.org/2000/svg"><path d="M1344 864q0-14-9-23l-352-352q-9-9-23-9t-23 9l-351 351q-10 12-10 24 0 14 9 23t23 9h224v352q0 13 9.5 22.5t22.5 9.5h192q13 0 22.5-9.5t9.5-22.5v-352h224q13 0 22.5-9.5t9.5-22.5zm640 288q0 159-112.5 271.5t-271.5 112.5h-1088q-185 0-316.5-131.5t-131.5-316.5q0-130 70-240t188-165q-2-30-2-43 0-212 150-362t362-150q156 0 285.5 87t188.5 231q71-62 166-62 106 0 181 75t75 181q0 76-41 138 130 31 213.5 135.5t83.5 238.5z"/></svg>'

export default {
  components: { Dropzone },
    props: {
        data: {
            type: Object,
            default: () => {},
        },
        options: Object,
    },
    computed: {
        filename() {
            return this.dataLoaded.src.split("/").pop();
        },
        uploadURL () {
            return this.options.image.uploadURL;
        },
        dropzoneOptions () {
          return {
            // custom dropzone options
            ...this.options.image.dropzoneOptions,

            // vue2-dropzone config
            id: `${this._uid}vwdropzone`,
            url: this.uploadURL,
            autoProcessQueue: this.uploadURL !== 'None',
            dictDefaultMessage: `<i class="fa">${UPLOAD_ICON}</i><br>Replace Image`,
            addRemoveLinks: false,
          }
        }
    },
    data() {
        return {
            dataLoaded: this.data,
            sendBack: {
                newUrl: '',
                alignment: this.data.alignment,
                original: this.data.target,
                width: this.data.width,
                height: this.data.height,
            },
            dropzoneContent: `Replace<br /><br /><svg fill="#666" width="26" height="24" viewBox="0 0 2048 1792" xmlns="http://www.w3.org/2000/svg"><path d="M1344 864q0-14-9-23l-352-352q-9-9-23-9t-23 9l-351 351q-10 12-10 24 0 14 9 23t23 9h224v352q0 13 9.5 22.5t22.5 9.5h192q13 0 22.5-9.5t9.5-22.5v-352h224q13 0 22.5-9.5t9.5-22.5zm640 288q0 159-112.5 271.5t-271.5 112.5h-1088q-185 0-316.5-131.5t-131.5-316.5q0-130 70-240t188-165q-2-30-2-43 0-212 150-362t362-150q156 0 285.5 87t188.5 231q71-62 166-62 106 0 181 75t75 181q0 76-41 138 130 31 213.5 135.5t83.5 238.5z"/></svg>`,
            thumb: this.data.src,
        }
    },
    methods: {
        cancelUpdateImage(event) {
            this.sendBack = {
                newUrl: this.dataLoaded.src,
                alignment: '',
                original: this.data.target,
                width: this.data.width,
                height: this.data.height,
            };
            this.$emit('returnImage', this.sendBack);
        },
        updateImage(event) {
            if (this.sendBack.newUrl == '') {
                this.sendBack.newUrl = this.dataLoaded.src;
            }
            var originalWidth = parseFloat(this.data.width);
            var newWidth = parseFloat(this.sendBack.width);
            var difference = originalWidth - newWidth;
            this.sendBack.height = this.data.height + difference;
            this.$emit('returnImage', this.sendBack);
        },
        fileUploaded (file, r) {
            this.thumb = r;
            this.sendBack.newUrl = r;
        },
        fileAdded (file) {
            if (file && this.uploadURL !== "None")
            return;

            const reader = new FileReader();
            reader.addEventListener("load", () => {
                this.thumb = reader.result;
                this.sendBack.newUrl = reader.result;
            }, false);

            reader.readAsDataURL(file);
        }
    }
}
</script>

<style>
.dz-image {
    position: absolute;
    left: 200px;
    top: 40px;
}
.dz-image img {
    padding: 0px;
    margin: 0px;
    max-width: 100px;
    max-height: 100px;
}
.dz-details, .dz-success-mark, .dz-error-mark, .dz-error-message, .dz-image {
    display: none;
}
.modal__wrap {
    width: 100%;
    height: 100%;
    background-color: #f6f6f6;
    padding: 10px;
    overflow-y: auto;
}
.loaded_target {
    max-width: 180px;
    max-height: 180px;
    float: left;
}
.loaded_controls {
    margin-left: 20px;
    text-align: left;
    float: left;
}
.dropzone-custom-content {
    text-align: center;
    width: 100px;
    cursor: pointer;
}
.column {
  float: left;
  width: 25%;
  padding: 10px;
  height: 100px;
  text-align: center;
  border-right: 1px dashed black;
}
.colright {
    border-right: 0px;
}

/* Clear floats after the columns */
.row:after {
  content: "";
  display: table;
  clear: both;
}
.upim_btn {
    padding: 10px;
}
.closeit {
    float: right;
    cursor: pointer;
}
.clearalignment {
    font-size: 10px;
    cursor: pointer;
    color: rgb(67, 106, 165);
}
</style>