<template>
    <div>
      <h1>Bài 2</h1>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import axios from 'axios';
  
  const students = ref([]);
  
  const getAllStudent = async () => {
    try {
      const response = await axios.get('http://localhost:8080/students');
      students.value = response.data;
      console.log(students.value); 
    } catch (error) {
      console.error("Có lỗi xảy ra khi lấy danh sách sinh viên:", error);
    }
  };
  
  onMounted(() => {
    getAllStudent(); 
  });
  </script>
  
  <style>
 
  </style>