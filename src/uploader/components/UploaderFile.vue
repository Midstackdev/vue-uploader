<template>
    <div class="bg-white rounded-lg p-4 flex items-stretch mb-1">
        <div>
            <UploadProgress :progress="progress"/>
        </div>
        <div class="flex flex-col justify-between">
            <div class="mb-2">
                <div class="font-medium mr-3 text-gray-700 leading-tight">
                    {{upload.file.name}}
                </div>

                <div class="text-gray-600 text-sm leading-tight">
                    {{ sizeDisplay }} MB
                </div>
            </div>

            <div class="text-gray-600 text-sm align-baseline">
                <template v-if="state === states.WAITING">Waiting</template>
                <template v-if="state === states.COMPLETED">Complete</template>
                <template v-if="state === states.FAILED">Failed</template>
                <template v-if="state === states.CANCELLED">Cancelled</template>
                <template v-if="state === states.UNSUPPORTED">Sorry this file is unsupported</template>
                <template v-if="state === states.UPLOADING">
                    <a 
                    href="#"
                    class="text-blue-500"
                    @click.prevent="cancel" 
                    >Cancel</a>
                </template>
            </div>
        </div>
    </div>
</template>

<script>
    import states from '@/uploader/states'
    import axios from 'axios'
    import UploadProgress from './UploadProgress'

    export default {
        components: {
            UploadProgress
        },
        props: {
            upload: {
                required: true,
                type: Object
            },

            baseURL: {
                required: true,
                type: String
            },

            endpoint: {
                required: true,
                // type: String
            }
        },

        data () {
            return {
                progress: 0,
                state: null,
                axios: {
                    cancel: null
                },
                states,
            }
        },

        watch: {
            progress (progress) {
                this.$emit('progress', {
                    id: this.upload.id,
                    progress
                })
            },
            state (state) {
                this.$emit('change', {
                    id: this.upload.id,
                    state
                })

                switch (state) {
                    case states.CANCELLED:
                    case states.FAILED:
                        this.progress = 0
                        break;
                }
            }
        },

        computed: {
            sizeDisplay () {
                return (this.upload.file.size / 1000000).toFixed(2)
            }
        },

        methods: {
            cancel () {
                this.axios.cancel()
            },
            makeFormData (file) {
                const form = new FormData()
                form.append('file', file, file.name)
                return form
            },

            handleUploadProgress (e) {
                this.progress = Math.round((e.loaded * 100) / e.total)
            },

            startUpload () {
                this.state = states.UPLOADING

                axios.post(this.endpoint, this.makeFormData(this.upload.file), {
                    baseURL: this.baseURL,
                    onUploadProgress: this.handleUploadProgress,
                    cancelToken: new axios.CancelToken((token) => {
                        this.axios.cancel = token
                    })
                }).then(() => {
                    this.state = states.COMPLETED
                }).catch((e) => {
                    if(e instanceof axios.Cancel) {
                        return this.state = states.CANCELLED
                    }
                    this.state = states.FAILED
                })
            }
        },

        mounted () {
            if (this.endpoint === null) {
                return this.state = states.UNSUPPORTED
            }
            this.state = states.WAITING

            this.startUpload()
        }
    }
</script>