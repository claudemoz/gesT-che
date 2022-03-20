<template>
  <el-row>
    <el-col :xs="12" :span="15" :lg="18">
      <el-input
        @keyup.enter="toggleTask"
        v-model="taskname"
        placeholder="Nom de votre tâche"
      ></el-input>
    </el-col>

    <el-col :xs="12" :span="9" :lg="6" class="actions">
      <strong>{{ currentDuration }}</strong>
      <el-button
        type="danger"
        v-if="!isTaskInProgress"
        @click="startTask"
        size="large"
        :icon="VideoPlay"
        circle
      ></el-button>
      <el-button
        type="success"
        v-else
        @click="stopTask"
        size="large"
        :icon="CircleCheckFilled"
        circle
      ></el-button>
    </el-col>

  </el-row>
</template>
<script>
import { CircleCheckFilled,Select,VideoPlay,VideoPause,} from "@element-plus/icons-vue";
import { shallowRef } from 'vue'

export default {
  data() {
    return {
      VideoPlay: shallowRef(VideoPlay),
      VideoPause: shallowRef(VideoPause) ,
      CircleCheckFilled: shallowRef(CircleCheckFilled),
      tsFormatter: Intl.DateTimeFormat("fr", {
        hour: "2-digit",
        minute: "2-digit",
      }),
      taskname: "",
      isTaskInProgress: false,
      startTime: null,
      nowTime: null,
      intervalEvrySeconde: null,
      errorMsg: null,
    };
  },

  computed: {
    currentDuration() {
      if (this.startTime && this.nowTime) {
        return this.durationBetweenTimestamps(this.startTime, this.nowTime);
      } else {
        return "00:00:00";
      }
    },
  },

  watch: {
    isTaskInProgress(isInProgress) {
      if (isInProgress) {
        this.intervalEvrySeconde = setInterval(() => {
          this.nowTime = Date.now();
        }, 1000);
      } else {
        clearInterval(this.intervalEvrySeconde);
      }
    },
  },

  methods: {
    startTask() {
      // vérification
      if (this.taskname.length == 0) {
        this.errorMsg = "Le nom d'une tache ne pas être vide";
        return;
      } else if (this.isTaskInProgress) {
        this.errorMsg = "Une tache est déjà en cours";
        return;
      } else {
        this.errorMsg = null;
      }
      // Début de la tâche
      this.isTaskInProgress = true;
      this.startTime = Date.now();
      this.nowTime = Date.now();
      // this.intervalEvrySeconde = setInterval(() => {
      //     this.nowTime = Date.now();
      // }, 1000);
      // console.log(this.startTime);
    },
    stopTask() {
      if (!this.isTaskInProgress) {
        this.errorMsg = "Aucune tache est déjà en cours";
        return;
      }

      // envoie de la tache
      this.$emit("newTask", {
        name: this.taskname,
        startTime: this.startTime,
      });

      // Fin de la tache
      // clearInterval(this.intervalEvrySeconde)
      this.isTaskInProgress = false;
      this.errorMsg = null;
      this.nowTime = null;
      this.taskname = "";

      // console.log(this.tasks);
    },
    restartTask(newTaskname) {
      //Arrêt de la tache en cours
      if (this.isTaskInProgress) {
        this.stopTask();
      }
     
      // Attente la mise à jour du DOM puis execution de la nouvelle tache
      this.$nextTick(function () {
        this.taskname = newTaskname;
        this.startTask();
      });
      //
      console.log("retartTask", newTaskname);
    },

    toggleTask() {
      if (this.isTaskInProgress) {
        this.stopTask();
      } else {
        this.startTask();
      }
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
  },
};
</script>

<style lang="scss" scoped>
.el-row {
  margin-top: 10px;
}
.el-input .el-input--default {
  border: none !important;
}

strong {
  display: inline-block;
  margin-left: 10px;
}

.actions {
  text-align: right;
  padding-right: 20px;
  strong {
    padding-right: 20px;
  }
}
</style>
