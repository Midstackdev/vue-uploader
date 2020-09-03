<template>
    <div>
        <UploaderForm 
            @chosen="handleFilesChosen"
        />
        <UploaderFile
            v-for="(upload, index) in uploads"
            :key="index"
            :upload="upload"
            :baseURL="options.baseURL"
            :endpoint="determineEndpointFor(upload.file.type)"
        />
    </div>
</template>

<script>
    import options from '@/uploader/options'
    import UploaderForm from './UploaderForm'
    import UploaderFile from './UploaderFile'
    import get from 'lodash.get'

    export default {
        components: {
            UploaderForm,
            UploaderFile
        },

        props: {
            options: {
                required: false,
                type: Object,
                default: () => options
            },

            handlers: {
                required: true,
                type: Object
            }
        },

        data () {
            return {
                uploads: []
            }
        },

        methods: {
            handleFilesChosen (files) {
                // files.forEach((file) => {
                //     this.uploads.push({
                //         file
                //     })
                // })
                
                this.uploads.push(...Array.from(files).map((file) => {
                    return {
                        file
                    }
                }))

                // console.log(this.uploads)
            },

            determineEndpointFor (fileType) {
                return get(this.handlers[fileType], 'endpoint', null)
            }
        }
    }
</script>