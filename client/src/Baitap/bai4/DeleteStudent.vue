<template>
    <div>
        <h1>Bài 4</h1>
        <h2>Xóa Sinh Viên</h2>
        <input v-model="studentId" placeholder="Nhập ID sinh viên" />
        <button @click="removeById">Xóa Sinh Viên</button>
        <div v-if="message">{{ message }}</div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import axios from 'axios';

  const studentId = ref('');
  const message = ref('');
  
  const removeById = async () => {
    try {
      const response = await axios.delete(`http://localhost:8080/students/${studentId.value}`);
      console.log(response.data); 
      message.value = 'Sinh viên đã được xóa thành công!';
    } catch (error) {
      console.error("Có lỗi xảy ra khi xóa sinh viên:", error);
      message.value = 'Không thể xóa sinh viên. Vui lòng kiểm tra lại ID.';
    }
  };
  </script>
  
  <style>
 
  </style>