<template>
    <div>
        <UploaderForm 
            @chosen="handleFilesChosen"
        />
        <UploaderFile
            v-for="(upload, index) in uploads"
            :key="index"
            :upload="upload"
        />
    </div>
</template>

<script>
    import UploaderForm from './UploaderForm'
    import UploaderFile from './UploaderFile'

    export default {
        components: {
            UploaderForm,
            UploaderFile
        },

        props: {
            options: {
                required: false,
                type: Object,
                default () {
                    return {
                        baseURL: ''
                    }
                }
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

                console.log(this.uploads)
            }
        }
    }
</script>