<template>
    <div>
        <UploaderForm 
            @chosen="handleFilesChosen"
        />
        <div class="mb-4 flex justify-between px-4 text-gray-600 text-sm">
            <span>{{ this.uploads.length }} uploads ({{ currentUploadCount }} in progress / {{ completedUploadCount }} completed)</span>
            <span>{{ overallProgress }}%</span>
        </div>
        <UploaderFile
            v-for="(upload, index) in uploads"
            :key="index"
            :upload="upload"
            :baseURL="options.baseURL"
            :endpoint="determineEndpointFor(upload.file.type)"
            @progress="handleUploadProgress"
            @change="handleUploadChange"
        />
    </div>
</template>

<script>
    import options from '@/uploader/options'
    import states from '@/uploader/states'
    import UploaderForm from './UploaderForm'
    import UploaderFile from './UploaderFile'
    import { v4 as uuid} from 'uuid'
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

        computed: {
            currentUploadCount () {
                return this.uploads.filter((upload) => upload.uploading).length
            },
            completedUploadCount () {
                return this.uploads.filter((upload) => upload.complete).length
            },
            overallProgress () {
                if(this.uploads.length === 0) {
                    return 0
                }

                let uploads = this.uploads.filter(upload => !upload.cancelled && !upload.failed)
                
                if(uploads.length === 0) {
                    return 0
                }
                return parseInt(uploads.reduce((a, b) => a + b.progress, 0) / uploads.length)
            }
        },

        methods: {
            handleUploadChange (e) {
                switch (e.state) {
                    case states.UPLOADING:
                        this.uploads = this.uploads.map((upload) => {
                            if(e.id === upload.id) {
                                upload.uploading = true
                            }
                            return upload
                        })
                        break;
                    case states.COMPLETED:
                        this.uploads = this.uploads.map((upload) => {
                            if(e.id === upload.id) {
                                upload.uploading = false
                                upload.complete = true
                            }
                            return upload
                        })
                        break;
                    case states.CANCELLED:
                        this.uploads = this.uploads.map((upload) => {
                            if(e.id === upload.id) {
                                upload.uploading = false
                                upload.cancelled = true
                            }
                            return upload
                        })
                        break;
                    case states.FAILED:
                        this.uploads = this.uploads.map((upload) => {
                            if(e.id === upload.id) {
                                upload.uploading = false
                                upload.failed = true
                            }
                            return upload
                        })
                        break;
                }
            },
            handleUploadProgress (e) {
                this.uploads = this.uploads.map((upload) => {
                    if(e.id === upload.id) {
                        upload.progress = e.progress
                    }
                    return upload
                })
            },
            handleFilesChosen (files) {    
                this.uploads.push(...Array.from(files).map((file) => {
                    return {
                        id: uuid(),
                        progress: 0,
                        uploading: false,
                        complete: false,
                        cancelled: false,
                        failed: false,
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