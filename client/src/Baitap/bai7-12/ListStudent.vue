<template>
    <div class="student-management">
      <Loading :isLoading="loading" />
      <h2>Quản lý sinh viên</h2>
      <table>
        <thead>
          <tr>
            <th>Tên sinh viên</th>
            <th>Email</th>
            <th>Địa chỉ</th>
            <th>Số điện thoại</th>
            <th>Hành động</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="student in paginatedStudents" :key="student.id">
            <td>{{ student.name }}</td>
            <td>{{ student.email }}</td>
            <td>{{ student.address }}</td>
            <td>{{ student.phone }}</td>
            <td>
              <button @click="showEditStudentModal(student)" class="edit-button">✏️</button>
              <button @click="confirmDelete(student)" class="delete-button">❌</button>
            </td>
          </tr>
        </tbody>
      </table>
  
      <div class="pagination">
        <button @click="prevPage" :disabled="currentPage === 1">Trước</button>
        <span>Trang {{ currentPage }} / {{ totalPages }}</span>
        <button @click="nextPage" :disabled="currentPage === totalPages">Sau</button>
      </div>
  
      <p>Hiện có {{ currentStudentsCount }} / {{ totalStudents }} sinh viên</p>
      <button @click="showAddStudentModal" class="add-button">Thêm mới sinh viên</button>
  
      <!-- Modal thêm sinh viên -->
      <b-modal v-model="showAddModal" title="Thêm mới sinh viên" ok-title="Thêm" cancel-title="Hủy" @ok="addStudent">
        <form @submit.prevent="addStudent">
          <div>
            <label>Tên sinh viên:</label>
            <input v-model="newStudent.name" type="text" required />
            <span v-if="errors.add.name" class="error">{{ errors.add.name }}</span>
          </div>
          <div>
            <label>Email:</label>
            <input v-model="newStudent.email" type="email" required />
            <span v-if="errors.add.email" class="error">{{ errors.add.email }}</span>
          </div>
          <div>
            <label>Địa chỉ:</label>
            <input v-model="newStudent.address" type="text" />
          </div>
          <div>
            <label>Số điện thoại:</label>
            <input v-model="newStudent.phone" type="text" @input="validatePhone('add')" />
            <span v-if="errors.add.phone" class="error">{{ errors.add.phone }}</span>
          </div>
        </form>
      </b-modal>
  
      <!-- Modal sửa thông tin sinh viên -->
      <b-modal v-model="showEditModal" title="Sửa thông tin sinh viên" ok-title="Lưu" cancel-title="Thoát" @ok="updateStudent">
        <form @submit.prevent="updateStudent">
          <div>
            <label>Tên sinh viên:</label>
            <input v-model="editStudent.name" type="text" required />
            <span v-if="errors.edit.name" class="error">{{ errors.edit.name }}</span>
          </div>
          <div>
            <label>Email:</label>
            <input v-model="editStudent.email" type="email" required />
            <span v-if="errors.edit.email" class="error">{{ errors.edit.email }}</span>
          </div>
          <div>
            <label>Địa chỉ:</label>
            <input v-model="editStudent.address" type="text" />
          </div>
          <div>
            <label>Số điện thoại:</label>
            <input v-model="editStudent.phone" type="text" @input="validatePhone('edit')" />
            <span v-if="errors.edit.phone" class="error">{{ errors.edit.phone }}</span>
          </div>
        </form>
      </b-modal>
  
      <!-- Modal xác nhận xóa -->
      <b-modal v-model="showModal" title="Xóa sinh viên" ok-title="Xóa" cancel-title="Hủy" @ok="deleteStudent">
        <p>Bạn chắc chắn muốn xóa sinh viên <strong>{{ studentToDelete?.name }}</strong>?</p>
      </b-modal>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, computed } from 'vue';
  import axios from 'axios';
  import Loading from './Loading.vue'; 
  
  const students = ref([]);
  const showModal = ref(false);
  const showAddModal = ref(false);
  const showEditModal = ref(false);
  const studentToDelete = ref(null);
  const newStudent = ref({
    name: '',
    email: '',
    address: '',
    phone: ''
  });
  const editStudent = ref({});
  const errors = ref({ add: {}, edit: {} });
  const loading = ref(false); 
  
  const currentPage = ref(1);
  const pageSize = 10;
  const totalStudents = ref(0);
  const totalPages = computed(() => Math.ceil(totalStudents.value / pageSize));
  
  const currentStudentsCount = computed(() => {
    const start = (currentPage.value - 1) * pageSize + 1;
    const end = Math.min(currentPage.value * pageSize, totalStudents.value);
    return `${start} - ${end}`;
  });
  
  const paginatedStudents = computed(() => {
    const start = (currentPage.value - 1) * pageSize;
    return students.value.slice(start, start + pageSize);
  });
  
  const fetchStudents = async () => {
    loading.value = true; 
    try {
      const response = await axios.get(` http://localhost:8080/students2?page=${currentPage.value}&limit=${pageSize}`);
      students.value = response.data.students;
      totalStudents.value = response.data.total;
    } catch (error) {
      console.error('Error fetching students:', error);
    } finally {
      loading.value = false; 
    }
  };
  
  const showAddStudentModal = () => {
    newStudent.value = { name: '', email: '', address: '', phone: '' };
    errors.value.add = {};
    showAddModal.value = true;
  };
  
  const showEditStudentModal = (student) => {
    editStudent.value = { ...student };
    errors.value.edit = {};
    showEditModal.value = true;
  };
  
  const validatePhone = (mode) => {
    const student = mode === 'edit' ? editStudent.value : newStudent.value;
    if (student.phone && isNaN(student.phone)) {
      errors.value[mode].phone = 'Số điện thoại chỉ được phép nhập số';
    } else {
      errors.value[mode].phone = '';
    }
  };
  
  const addStudent = async () => {
    loading.value = true; 
    errors.value.add = {};
    if (!newStudent.value.name) {
      errors.value.add.name = 'Tên sinh viên không được để trống';
    }
    if (!newStudent.value.email) {
      errors.value.add.email = 'Email không được để trống';
    } else if (!/\S+@\S+\.\S+/.test(newStudent.value.email)) {
      errors.value.add.email = 'Email không đúng định dạng';
    }
    if (errors.value.add.name || errors.value.add.email || errors.value.add.phone) return;
  
    try {
      await axios.post(' http://localhost:8080/students2', newStudent.value);
      showAddModal.value = false;
      fetchStudents();
    } catch (error) {
      console.error('Error adding student:', error);
    } finally {
      loading.value = false; 
    }
  };
  
  const updateStudent = async () => {
    loading.value = true; 
    errors.value.edit = {};
    if (!editStudent.value.name) {
      errors.value.edit.name = 'Tên sinh viên không được để trống';
    }
    if (!editStudent.value.email) {
      errors.value.edit.email = 'Email không được để trống';
    } else if (!/\S+@\S+\.\S+/.test(editStudent.value.email)) {
      errors.value.edit.email = 'Email không đúng định dạng';
    }
    if (editStudent.value.phone && isNaN(editStudent.value.phone)) {
      errors.value.edit.phone = 'Số điện thoại chỉ được phép nhập số';
    }
    if (errors.value.edit.name || errors.value.edit.email || errors.value.edit.phone) return;
  
    try {
      await axios.put(` http://localhost:8080/students2/${editStudent.value.id}`, editStudent.value);
      showEditModal.value = false;
      fetchStudents();
    } catch (error) {
      console.error('Error updating student:', error);
    } finally {
      loading.value = false;
    }
  };
  
  const confirmDelete = (student) => {
    studentToDelete.value = student;
    showModal.value = true;
  };
  
  const deleteStudent = async () => {
    loading.value = true; 
    try {
      await axios.delete(` http://localhost:8080/students2/${studentToDelete.value.id}`);
      showModal.value = false;
      fetchStudents();
    } catch (error) {
      console.error('Error deleting student:', error);
    } finally {
      loading.value = false; 
    }
  };
  
  const prevPage = () => {
    if (currentPage.value > 1) {
      currentPage.value--;
      fetchStudents();
    }
  };
  
  const nextPage = () => {
    if (currentPage.value < totalPages.value) {
      currentPage.value++;
      fetchStudents();
    }
  };
  
  onMounted(fetchStudents);
  </script>
  
  <style scoped>
  .student-management {
    padding: 20px;
    background-color: #f8f9fa;
  }
  
  h2 {
    color: #343a40;
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }
  
  th, td {
    border: 1px solid #dee2e6;
    padding: 10px;
    text-align: left;
  }
  
  th {
    background-color: #007bff;
    color: white;
  }
  
  tr:nth-child(even) {
    background-color: #f2f2f2;
  }
  
  .edit-button, .delete-button, .add-button {
    background-color: #007bff;
    color: white;
    border: none;
    padding: 5px 10px;
    cursor: pointer;
    border-radius: 4px;
    font-size: 14px;
  }
  
  .edit-button:hover {
    background-color: #0056b3;
  }
  
  .delete-button, .add-button {
    background-color: #dc3545;
  }
  
  .delete-button:hover, .add-button:hover {
    background-color: #c82333;
  }
  
  .error {
    color: red;
    font-size: 12px;
  }
  
  .pagination {
    margin-top: 20px;
    text-align: center;
  }
  
  .pagination button {
    margin: 0 5px;
    padding: 5px 10px;
  }
  </style>