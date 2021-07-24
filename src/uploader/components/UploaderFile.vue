<template>
    <div class="bg-white rounded-lg p-4 flex items-stretch mb-1">
        <div>
            {{progress}}
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
                <template v-if="state === states.UPLOADING">Uploading</template>
                <template v-if="state === states.UNSUPPORTED">Sorry this file is unsupported</template>
            </div>
        </div>
    </div>
</template>

<script>
    import states from '@/uploader/states'
    import axios from 'axios'

    export default {
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
                states,
            }
        },

        computed: {
            sizeDisplay () {
                return (this.upload.file.size / 1000000).toFixed(2)
            }
        },

        methods: {
            makeFormData (file) {
                const form = new FormData()
                form.append('file', file, file.name)
                return form
            },

            startUpload () {
                this.state = states.UPLOADING

                axios.post(this.endpoint, this.makeFormData(this.upload.file), {
                    baseURL: this.baseURL
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