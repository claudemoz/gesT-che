<template>
    <el-button type="default" size="large" @click="sendCopyTaskname" :icon="CopyDocument" circle />
    <el-button type="primary" size="large" @click="sendRestart" :icon="VideoPlay" circle />
    <el-button type="danger" size="large" @click="sendDelete" :icon="Delete" circle />
</template>

<script>
import { CopyDocument, Delete, VideoPlay} from "@element-plus/icons-vue";
import { shallowRef } from 'vue'
    export default {
        data() {
            return {
                CopyDocument:shallowRef(CopyDocument),
                Delete:shallowRef(Delete),
                VideoPlay:shallowRef(VideoPlay)
            }
        },
        props: {
            taskID: {
                type: String,
                required: true 
            },
        },
        emits:['restart', 'delete', 'copyTaskname'],
        methods: {
            sendRestart() {
                this.$emit('restart', this.taskID)
            },
            sendDelete(){
                this.$emit('delete', this.taskID)
            },
            sendCopyTaskname(){
                this.$emit('copyTaskname')
            }
        },
    }
</script>

<style lang="scss" scoped>

</style>