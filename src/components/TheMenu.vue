<template>
    <el-menu default-active="1">

        <h3>GesTâche </h3>
        <p>version 1.0</p>

        <el-menu-item  index="1" >
        <el-icon><Timer /></el-icon>
        <span>Tracker</span>
        </el-menu-item>

        <el-menu-item index="2" disabled>
        <el-icon><Folder-opened /></el-icon>
        <span>Catégories</span>
        </el-menu-item>

        <el-menu-item index="3" disabled>
        <el-icon><Setting /></el-icon>
        <span>Paramètres</span>
        </el-menu-item>
    </el-menu>
</template>

<script>
import { FolderOpened,Timer, Message, Menu as IconMenu, Setting } from '@element-plus/icons-vue'
    export default{
        components:{
            FolderOpened,Timer, Message,IconMenu, Setting
        }
    }
</script>

<style  scoped>
    
.el-menu {
  border-right: none;
}
h3{
    text-align: center;
}
</style>