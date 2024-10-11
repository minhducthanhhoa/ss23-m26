<template>
    <div>
      <h1>Bài 3</h1>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import axios from 'axios';
  
  const student = ref(null);

  const getStudentById = async (id) => {
    try {
      const response = await axios.get(`http://localhost:8080/students/${id}`);
      if (response.data) {
        student.value = response.data;
        console.log(student.value); 
      } else {
        console.log('Không tìm thấy bản ghi');
      }
    } catch (error) {
      console.error("Có lỗi xảy ra khi lấy thông tin sinh viên:", error);
    }
  };
  
  getStudentById(2);
  </script>
  
  <style>
 
  </style>