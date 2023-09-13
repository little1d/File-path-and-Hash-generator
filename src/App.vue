<script>
import { ref } from 'vue';

const file = ref(null);

const files = ref([]);
// const handleFileUpload = (event) => {
//   file.value = event.target.files[0];
// };

// const uploadFile = () => {
//   if (file.value) {
//     // 在这里执行文件上传的逻辑
//     console.log("上传文件:", file.value);
//     // 可以使用axios或其他库发送文件到服务器
//   } else {
//     console.log("请选择要上传的文件");
//   }
// };
// ;
</script>


<template>
  <div class="page">
    <div class="container">
    <!-- <input type="file" @change="handleFileUpload" />
    <button @click="uploadFile">上传文件</button> -->
    <div class="header">
      <div class="left"><h5 class="google-font">Upload file(s)</h5></div>
      <div class="right"></div>
    </div>
      <div class="dragzone">
        <span class="dropzone-text">Drag files/folders here or click to browse from your computer</span>
      </div>
    <!-- 创建上传记录 -->
    <!-- <div v-for="file in files">

    </div> -->
    </div>
  </div>
  
</template>

<style scoped>

*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.page{
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  transform: scale(1.3);
}

.container{
  flex-direction: column;
  border: 2px solid #e5e7eb;
  border-radius: 0.75rem;
  padding: 1rem;
}

.header{
  display: flex;
  margin-bottom: 1rem;
}
.left {
  cursor: pointer;
  width: 20%;
}

.right {
  flex-grow: 1; /* 占据剩余空间 */
  height: 50px;
  border-left: 1px solid #e5e7eb; /* 只保留左边框 */
  border-bottom: 1px solid #e5e7eb; /* 只保留下边框 */
  background: linear-gradient(to bottom, #ffffff, #f8f9fa); /* 添加渐变色 */
  margin-top: -15px;
  margin-right: -15px;
}


.dragzone{
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 200px;
  border: 2px dashed #e5e7eb;
  border-radius: 0.75rem;
  cursor: pointer;
}

.dragzone:hover{
  background-color: rgb(249 250 251);
}


.google-font,span{
  font-family: 'Poppins',sans-serif;
}

.dragzone span{
  padding: 2rem;
}

.dragzone:hover span{
  color: rgb(107, 114, 128);
}
</style>