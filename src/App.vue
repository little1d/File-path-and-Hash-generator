<script setup>
import { ref } from 'vue';
import { createSHA256 } from 'hash-wasm'

const progress = ref(0);
const files = ref([]);
// 处理点击事件
const handleFileClick = () => {
  const input = document.createElement('input');
  input.type = 'file';
  input.addEventListener('change', (event) => {
    const file = event.target.files[0];
    calculateSHA256(file);
  })
  input.click();
  handleUploadProgress();
}

// 定义一个计算SHA256编码的函数，接收一个文件作为参数
const calculateSHA256 = (file) => {
  // 创建一个fileReader对象，用于读取文件内容 fileReader
  const reader = new FileReader();
  // reader.onload事件，当文件读取完成时触发
  reader.onload = async (event) => {
    // 获取文件数据
    const fileData = event.target.result;
    // 创建CreateSHA256实例
    const sha256 = await createSHA256();
    // 将文件数据转换为无符号8位整数数组
    const buffer = new Uint8Array(fileData);
    // 调用update方法，将文件数据传递更新 调用digest方法，以十六进制字符串的形式获取SHA256哈希值
    const hashValue = sha256.update(buffer).digest('hex');
    // console.log(hashValue);
    // 将文件的名称、路径、哈希值添加到file对象中
    files.value.push({
      name: file.name,
      path: file.path,
      hashValue: hashValue,
    })
  }
  // 最后，通过调用reader.readAsArrayBuffer(file)方法，将文件读取位ArrayBuffer
  reader.readAsArrayBuffer(file);
}

// 递归遍历文件夹中的文件
const traverseDirectory = (directory, parentPath) => {
  const reader = directory.createReader();
  reader.readEntries((entries) => {
    entries.forEach((entry) => {
      if (entry.isFile) {
        entry.file((file) => {
          file.path = `${parentPath}/${file.name}`
          calculateSHA256(file);
        });
      } else if (entry.isDirectory) {
        traverseDirectory(entry, `${parentPath}/${entry.name}`);
      }
    })
  })
}

const handleFileDrop = async (e) => {
  e.preventDefault();
  const items = e.dataTransfer.items;
  for (let i = 0; i < items.length; i++) {
    const item = items[i].webkitGetAsEntry();
    // 对于单个文件调用calculateSHA256
    if (item.isFile) {
      const file = items[i].getAsFile();
      calculateSHA256(file)
    }
    // 对于目录调用traverseDirectory
    else if (item.isDirectory) {
      traverseDirectory(item, item.name);
    }
  }
  handleUploadProgress(e);
}

// 监听文件上传进度
const handleUploadProgress = (event,file) => {
  if (event.lengthComputable) {
    const percent = Math.round((event.loaded / event.total) * 100);
    progress.value = percent;
    file.uploadProgress = percent;
  }
}
</script>


<template>
  <div class="page">
    <div class="container">

      <div class="header">
        <div class="left" @drop="handleFileDrop" @dragover.prevent>
          <h5 class="google-font">Upload file(s)</h5>
        </div>
        <div class="right"></div>
      </div>
      <!-- 当拖拽事件发生后，执行handleFileDrop函数逻辑 并阻止默认拖动行为-->
      <!-- 合并成同一个处理器函数 同时处理进度条与主业务 -->
      <div class="dragzone" @drop="handleFileDrop" @click="handleFileClick" @dragover.prevent>
        <span>Drag files/folders here or click to browse from your computer</span>
      </div>
      <div class="outputzone">
        <div class="fileInfo google-font" v-for="file in files" :key="file.name">
          <div class="fileName google-font">{{ file.name }}</div>
          <div class="filePath google-font">{{ file.path }} </div>
          <!-- 显示哈希值的前8位 -->
          <div class="hashValue">{{ file.hashValue.substring(0, 8) }}</div>
          <div class="progress-bar" v-if="progress > 0 && progress < 100"> >
            <div class="progress" :style="{ width: file.handleUploadProgress + '%' }"></div>
          </div>
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
  overflow: hidden;

}

.page {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 100vh;
  transform: scale(1);
}

.container {
  display: flex;
  width: 50%;
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
  border-left: 1px solid #e5e7eb;
  /* 只保留左边框 */
  border-bottom: 1px solid #e5e7eb;
  /* 只保留下边框 */
  background: linear-gradient(to bottom, #ffffff, #e1e2e3);
  /* 添加渐变色 */
  margin-bottom: -1rem;
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


.fileInfo {
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.progress-bar {
  width: 100%;
  height: 10px;
  background-color: #e5e7eb;
  border-radius: 5px;
  margin-top: 1rem;
}

.progress {
  height: 100%;
  background-color: #4b5563;
  border-radius: 5px;
  transition: width 0.3s ease-in-out;
}

.fileInfo {
  margin: 1rem;
  border: 2px dashed #cfd3db;
  border-radius: 20px;
}
</style>