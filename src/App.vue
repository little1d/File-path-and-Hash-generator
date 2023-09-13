<script setup>
import { ref } from 'vue';
const files = ref([]);
const hashValueData = ref(['1', '2', '3']);

const handleFileDrop = (e) => {
  //阻止默认行为 确保页面不会打开拖放的文件
  e.preventDefault();
  const fileList = e.dataTransfer.files;
  for (let i = 0; i < fileList.length; i++) {
    files.value.push(fileList[i]);
  }
}

const traverseDirectory = (directory,parentPath) => {
  const reader = directory.createReader();
  reader.readEntries((entries) => {
    entries.forEach((entry) => {
      if (entry.isFile) {
        entry.file((file) => {
          files.value.push({
            name: file.name,
            path: `${parentPath}/${file.name}`,
          });
        });
      } else if (entry.isDirectory) {
        traverseDirectory(entry,`${parentPath}/${entry.name}`);
      }
    })
  })
}

const handleFileDrop2 = (e) => {
  e.preventDefault();
  const items = e.dataTransfer.items;
  for (let i = 0; i < items.length; i++) {
    const item = items[i].webkitGetAsEntry();
    if (item.isFile) {
      item.file((file) => {
        this.files.push({
          name: file.name,
          path: file.webkitRelativePath,
        });
      });
    } else if (item.isDirectory) {
      traverseDirectory(item,item.name);
    }
  }
}


</script>


<template>
  <div class="page">
    <div class="container">

      <div class="header">
        <div class="left" @drop="handleFileDrop2" @dragover.prevent>
          <h5 class="google-font">Upload file(s)</h5>
        </div>
        <div class="right"></div>
      </div>
      <!-- 当拖拽事件发生后，执行handleFileDrop函数逻辑 并阻止默认拖动行为-->
      <div class="dragzone" @drop="handleFileDrop2" @dragover.prevent>
        <span>Drag files/folders here or click to browse from your computer</span>
      </div>
      <div class="outputzone">
        <div class="fileInfo" v-for="file in files" :key="file.name">
          <!-- <div class="fileName">{{ file.name }}</div> -->
          <div class="filePath">{{ file.path }}</div>
        </div>

      </div>
    </div>
  </div>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.page {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  transform: scale(1.2);
}

.container {
  flex-direction: column;
  border: 2px solid #e5e7eb;
  border-radius: 0.75rem;
  padding: 1rem;
}

.header {
  display: flex;
  margin-bottom: 1rem;
}

.left {
  cursor: pointer;
  width: 20%;
}

.right {
  flex-grow: 1;
  /* 占据剩余空间 */
  height: 50px;
  border-left: 1px solid #e5e7eb;
  /* 只保留左边框 */
  border-bottom: 1px solid #e5e7eb;
  /* 只保留下边框 */
  background: linear-gradient(to bottom, #ffffff, #e1e2e3);
  /* 添加渐变色 */
  margin-top: -15px;
  margin-right: -15px;
}


.dragzone {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 200px;
  border: 2px dashed #e5e7eb;
  border-radius: 0.75rem;
  cursor: pointer;
}

.dragzone:hover {
  background-color: rgb(249 250 251);
}


.google-font,
span {
  font-family: 'Poppins', sans-serif;
}

.dragzone span {
  padding: 2rem;
}

.dragzone:hover span {
  color: rgb(107, 114, 128);
}

.outputzone {
  width: 200px;
  height: 200px;
}</style>