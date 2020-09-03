<template>
    <div>
        <UploaderForm 
            @chosen="handleFilesChosen"
        />
    </div>
</template>

<script>
    import UploaderForm from './UploaderForm'

    export default {
        components: {
            UploaderForm
        },

        methods: {
            handleFilesChosen (files) {
                console.log(files)
            }
        }
    }
</script>