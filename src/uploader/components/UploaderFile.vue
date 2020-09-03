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
                Cancelled
            </div>
        </div>
    </div>
</template>

<script>
    
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
                progress: 0
            }
        },

        computed: {
            sizeDisplay () {
                return (this.upload.file.size / 1000000).toFixed(2)
            }
        },

        mounted () {
            console.log(this.endpoint)
        }
    }
</script>