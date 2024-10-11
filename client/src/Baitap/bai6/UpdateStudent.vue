<template>
    <div>
      <h2>Cập Nhật Sinh Viên</h2>
      <input v-model="student.id" placeholder="ID sinh viên" />
      <input v-model="student.student_name" placeholder="Tên sinh viên" />
      <input v-model="student.email" placeholder="Email" />
      <input v-model="student.address" placeholder="Địa chỉ" />
      <input v-model="student.phone" placeholder="Số điện thoại" />
      <select v-model="student.status">
        <option :value="true">Kích hoạt</option>
        <option :value="false">Ngừng kích hoạt</option>
      </select>
      <button @click="updateStudentById">Cập Nhật Sinh Viên</button>
      <div v-if="message">{{ message }}</div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import axios from 'axios';
  
  const student = ref({
    id: '',
    student_name: '',
    email: '',
    address: '',
    phone: '',
    status: true, 
  });

  const message = ref('');
  
  // Hàm để cập nhật sinh viên theo ID
  const updateStudentById = async () => {
    try {
      const response = await axios.put(`http://localhost:8080/students/${student.value.id}`, student.value);
      console.log(response.data);
      message.value = 'Sinh viên đã được cập nhật thành công!';
    } catch (error) {
      console.error("Có lỗi xảy ra khi cập nhật sinh viên:", error);
      message.value = 'Không thể cập nhật sinh viên. Vui lòng kiểm tra lại.';
    }
  };
  </script>
  
  <style>
 
  </style>