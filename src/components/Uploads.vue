<template>
    <div>
        <file v-for="file in files" :file="file"></file>
    </div>
</template>

<script>
import File from './File'
export default {
    props: [
        'files'
    ],
    components: {
        File
    }
}
</script>
