<template>
  <el-container>
    <el-aside width="200px">
      <el-scrollbar>
        <The-menu />
      </el-scrollbar>
    </el-aside>

    <el-container>
      <el-header height="60px">
        <TheTopTask ref="TheTopTask" @newTask="addTask($event)" />
      </el-header>

      <el-main>
        <el-scrollbar>
          <TheList
            :Taskloading="loading"
            :tasks="tasks"
            @restart="sendRestartTask($event)"
            @delete="deleteTask($event)"
          />
        </el-scrollbar>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import { v4 as uuid } from '@lukeed/uuid';

import * as TaskService from './services/TaskService.js'

import TheMenu from "./components/TheMenu.vue";
import TheTopTask from "./components/TheTopTask.vue";
import TheList from "./components/TheList.vue";
// import { ref } from 'vue'
// import { FolderOpened,Timer, Message, Menu as IconMenu, Setting } from '@element-plus/icons-vue'
export default {
  components: {
    TheMenu,
    TheTopTask,
    TheList,
  },

  data() {
    return {
      tasks: [],
      loading: true
    };
  },

  methods: {
    async addTask({ name, startTime }) {
      // Ajout de la tâche en locale
      this.tasks.unshift({
        id: uuid(),
        name,
        startTime,
        endTime: Date.now(),
      })
      // Mise è jour de toutes les tâches en API
      try {
        await TaskService.updateAll(this.tasks)
      } catch (e) {
        console.error(e);
      }
    },

    async deleteTask(taskID) {
      // Suppression de la tâche en locale
      this.tasks = this.tasks.filter((task) => task.id != taskID);

      // Mise è jour de toutes les tâches en API
      try {
          await TaskService.updateAll(this.tasks)
        } catch (e) {
          console.error(e);
        }
    },

    sendRestartTask(taskID) {
      //Récupération du nom de l'ancienne tache
      let newTaskname = null;
      this.tasks.forEach((task) => {
        if (task.id == taskID) {
          newTaskname = task.name;
        }
      });
        // Relancement de la tâche
      this.$refs.TheTopTask.restartTask(newTaskname);
    },
  },

  async created(){
    try {
      this.tasks = await TaskService.getAll()
    } catch (e) {
      console.error(e);
    }
    this.loading = false
  }
};

</script>

<style lang="scss">
#app {
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.el-header {
  /* padding: 0px !important; */
  background-color: #5da0f1;
  border-bottom: solid 1px #e6e6e6;
  color: var(--el-text-color-primary);
  .el-input .el-input__inner {
    border: none !important;
    height: 40px !important;
  }
}
.el-aside {
  width: 240px;
  color: var(--el-text-color-primary);
  background: #fff !important;
  border-right: solid 1px #e6e6e6;
  height: 100vh;
  box-sizing: border-box;
}

.el-main {
  padding: 0;
}
.toolbar {
  position: absolute;
  display: inline-flex;
  align-items: center;
  top: 50%;
  right: 20px;
  transform: translateY(-50%);
}
</style>
