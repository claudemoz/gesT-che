<template>
  <el-table v-loading="Taskloading" :data="tasks" row-key="id" empty-text="Aucune tâche" style="width: 100%">
    <el-table-column label="Tâche" prop="name" />

    <el-table-column label="Début et Fin" prop="">
      <template #header></template>
      <template #default="scope">
        {{ formatTimestamp(scope.row.startTime) }} - {{ formatTimestamp(scope.row.endTime) }}
      </template>
    </el-table-column>

    <el-table-column label="Durée" prop="">
      <template #header></template>
      <template #default="scope">
        {{ durationBetweenTimestamps(scope.row.startTime, scope.row.endTime) }}
      </template>
    </el-table-column>

    <el-table-column align="right">
      <template #default="scope">
        <TaskListActions 
          @copyTaskname="copyToClipboard(scope.row.name)" 
          @restart="sendRestart($event)"
          @delete="sendDelete($event)" 
          :taskID="scope.row.id" />
      </template>
    </el-table-column>
  </el-table>
</template>

<script>
import TaskListActions from './TaskListActions.vue';
export default {
  components: { 
      TaskListActions
    },
  data() {
    return {
      tsFormatter: Intl.DateTimeFormat("fr", {
        hour: "2-digit",
        minute: "2-digit",
      }),
    };
  },
  props: {
    tasks: {
      type: Array,
      default: [],
    },
    Taskloading: {
      type: Boolean,
      default: false
    },
  },
  emits:['restart', 'delete'],
  methods: {
    formatTimestamp(ts) {
      return this.tsFormatter.format(ts);
    },
    durationBetweenTimestamps(start, end) {
      // console.log(start, end)
      let seconds = Math.floor(end / 1000 - start / 1000);
      let minutes = Math.floor(seconds / 60);
      let hours = Math.floor(minutes / 60);
      seconds = seconds % 60;
      minutes = minutes % 60;
      return `${String(hours).padStart(2, "0")}:${String(minutes).padStart(
        2,
        "0"
      )}:${String(seconds).padStart(2, "0")}`;
    },

    sendRestart(data){
        this.$emit('restart', data)
    },
    sendDelete(data){
        this.$emit('delete', data)
    },
    copyToClipboard(text){
      navigator.clipboard.writeText(text);
    }
  },
};
</script>
