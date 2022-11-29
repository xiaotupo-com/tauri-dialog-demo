<script setup lang="ts">
import { ask, confirm, message, open, save } from '@tauri-apps/api/dialog';
import { homeDir } from '@tauri-apps/api/path';
import { ref, Ref } from 'vue';

const file_list : Ref<string[]> = ref([]);


async function testOpen() {
  const fileSelected = await open({
    directory: true, // 是为目录选择，如果为 true，则不能选择文件
    multiple: true, // 是否允许多选
    defaultPath: await homeDir(), // 默认目录
    filters: [
      {
        name: 'Image',
        extensions: ['png', 'jpg']
      }
    ]
  });

  if (Array.isArray(fileSelected)) {
    fileSelected.forEach((value, index)=>{
      file_list.value.push(value);
    });
  } else if (fileSelected === null) {
    message("您取消了文件选择", {title: "取消", type: "info"});
  } else {
    // 如果 multiple: false 时执行这里，否则不会执行到这里
    file_list.value.push(fileSelected);
  }
}

async function testSave() {
  const filePath = await save({
    title: "保存文件",
    defaultPath: await homeDir(),
    filters: [
      {
        name: 'Image',
        extensions: ['png', 'jpg']
      }
    ]
  });

  if (filePath != null) {
    console.log(filePath);
  }
}

</script>

<template>
  <div class="container">
    <h1>Tauri Dialog 例子</h1>
    <el-row class="mb-4">
      <el-button type="primary" @click="testOpen">打开对话框</el-button>
      <el-button type="primary" @click="testSave">保存文件</el-button>
    </el-row>
    <p>文件路径列表：
      <ul>
        <li v-for="f in file_list">{{ f }}</li>
      </ul>
    </p>
  </div>
</template>

<style scoped>

</style>
