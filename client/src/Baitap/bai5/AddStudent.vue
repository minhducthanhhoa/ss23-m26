<template>
    <div>
        <h1>Bài 5</h1>
        <h2>Thêm Mới Sinh Viên</h2>
        <input v-model="student.student_name" placeholder="Tên sinh viên" />
        <input v-model="student.email" placeholder="Email" />
        <input v-model="student.address" placeholder="Địa chỉ" />
        <input v-model="student.phone" placeholder="Số điện thoại" />
        <select v-model="student.status">
            <option :value="true">Kích hoạt</option>
            <option :value="false">Ngừng kích hoạt</option>
        </select>
        <button @click="createStudent">Thêm Sinh Viên</button>
        <div v-if="message">{{ message }}</div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import axios from 'axios';
  
  const student = ref({
    student_name: '',
    email: '',
    address: '',
    phone: '',
    status: true, 
  });
  
  const message = ref('');
  
  const createStudent = async () => {
    try {
      const response = await axios.post('http://localhost:8080/students', student.value);
      console.log(response.data); 
      message.value = 'Sinh viên đã được thêm thành công!';
      
      student.value = {
        student_name: '',
        email: '',
        address: '',
        phone: '',
        status: true,
      };
    } catch (error) {
      console.error("Có lỗi xảy ra khi thêm sinh viên:", error);
      message.value = 'Không thể thêm sinh viên. Vui lòng kiểm tra lại.';
    }
  };
  </script>
  
  <style>
 
  </style>