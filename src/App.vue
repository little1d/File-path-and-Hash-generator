<script setup>
import { ref } from 'vue';
import { createSHA256 } from 'hash-wasm'
const files = ref([]);

// async function run() {
//   const sha1 = await createSHA256();
//   sha1.init();

//   const hash = sha1.digest('binary'); // returns Uint8Array
//   console.log('SHA256:', hash);
// }

// run();

const handleFileClick = () =>{
  const input = document.createElement('input');
  input.type = 'file';
  input.addEventListener('change',(event)=>{
    const file = event.target.files[0];
    calculateSHA256(file);
  })
  input.click();
}

const calculateSHA256 = async (file) => {
  const reader = new FileReader();
  reader.onload = async (event) => {
    const fileData = event.target.result;
    const sha256 = await createSHA256();
    sha256.init()
    const buffer = new Uint8Array(fileData);
    const hashValue =  sha256.update(buffer).digest('hex');
    console.log(hashValue);
    files.value.push({
      name: file.name,
      path: file.webkitGetAsEntry,
      hashValue: hashValue,
    })
  }
  reader.readAsArrayBuffer(file);
}


// 递归遍历文件夹中的文件
const traverseDirectory = (directory, parentPath) => {
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
        traverseDirectory(entry, `${parentPath}/${entry.name}`);
      }
    })
  })
}

const handleFileDrop2 = async (e) => {
  e.preventDefault();
  const items = e.dataTransfer.items;
  for (let i = 0; i < items.length; i++) {
    const item = items[i].webkitGetAsEntry();
    if (item.isFile) {
      const file = items[i].getAsFile();
      calculateSHA256(file)
    }
    else if (item.isDirectory) {
      traverseDirectory(item, item.name);
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
      <div class="dragzone" @drop="handleFileDrop2" @click="handleFileClick" @dragover.prevent>
        <span>Drag files/folders here or click to browse from your computer</span>
      </div>
      <div class="outputzone">
        <div class="fileInfo" v-for="file in files" :key="file.name">
          <div class="fileName">{{ file.name }}</div>
          <div class="filePath">{{ file.path }} </div>
          <div class="hashValue">{{ file.hashValue }}</div>
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
  width: 100vw;
  height: 100vh;
  transform: scale(1);
}

.container {
  width: 100%;
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

}

.fileInfo {
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.hashValue{

}
</style>