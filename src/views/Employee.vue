<template>
  <div class="p-6 bg-gray-100 min-h-screen font-inter">
    <h3 class="text-xl font-semibold mb-4">Danh sách nhân viên</h3>

    <!-- Nút Thêm Mới -->
    <div class="mb-4 text-right">
      <button
        @click="openAddPopup"
        class="bg-[#2292A7] text-white px-4 py-2 rounded hover:bg-[#1b7e91]"
      >
        <i class="fa-solid fa-plus mr-1"></i> Thêm mới
      </button>
    </div>

    <!-- Bảng danh sách -->
    <div class="bg-white rounded-lg shadow-md p-6 w-full">
      <table class="w-full table-auto divide-y divide-gray-400">
        <thead class="bg-gray-50">
          <tr>
            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">ID</th>
            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Họ tên</th>
            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Email</th>
            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Số điện thoại</th>
            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Quyền</th>
            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Ngày đăng ký</th>
            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Hoạt động</th>
            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Hành động</th>
          </tr>
        </thead>
        <tbody class="bg-white divide-y divide-gray-200">
          <tr v-for="user in users" :key="user.id">
            <td class="px-4 py-2 text-sm text-gray-900">{{ user.id }}</td>
            <td class="px-4 py-2 text-sm text-gray-900">{{ user.fullName }}</td>
            <td class="px-4 py-2 text-sm text-gray-700">{{ user.email }}</td>
            <td class="px-4 py-2 text-sm text-gray-700">{{ user.phoneNumber }}</td>
            <td class="px-4 py-2 text-sm text-gray-700">{{ user.roleName }}</td>
            <td class="px-4 py-2 text-sm text-gray-700">{{ user.registrationDate }}</td>
            <td class="px-4 py-2 text-sm">
              <span :class="getStatusClass(user.isActive)">
                {{ user.isActive ? 'Đang hoạt động' : 'Ngưng hoạt động' }}
              </span>
            </td>
            <td class="px-4 py-2 text-sm text-right">
                <button @click="openEditPopup(user)" class="text-gray-400 mr-5 hover:text-blue-500 mr-3">
                  <i class="fa-solid fa-pen-to-square"></i>
                </button>
                <button class="text-gray-400 mr-1 hover:text-red-500">
                  <i class="fa-solid fa-trash"></i>
                </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Popup Form -->
    <div v-if="showPopup" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 z-50">
      <div class="bg-white p-6 rounded-lg w-[400px] shadow-lg">
        <h2 class="text-xl font-bold mb-4 text-[#2292A7]">
          {{ isEditMode ? 'Chỉnh sửa thông tin nhân viên' : 'Thêm nhân viên' }}
        </h2>
        <form @submit.prevent="saveChanges">
          <div class="mb-4">
            <label class="block text-gray-700">Họ tên</label>
            <input v-model="editedUser.fullName" type="text" class="w-full mt-1 p-2 border border-gray-300 rounded" />
          </div>
          <div class="mb-4">
            <label class="block text-gray-700">Email</label>
            <input v-model="editedUser.email" type="email" class="w-full mt-1 p-2 border border-gray-300 rounded" />
            <p v-if="errors.email" class="text-red-500 text-sm mt-1">{{ errors.email }}</p>
          </div>
          <div class="mb-4">
            <label class="block text-gray-700">Số điện thoại</label>
            <input v-model="editedUser.phoneNumber" type="text" class="w-full mt-1 p-2 border border-gray-300 rounded" />
            <p v-if="errors.phoneNumber" class="text-red-500 text-sm mt-1">{{ errors.phoneNumber }}</p>
          </div>
          <div class="mb-4">
            <label class="block text-gray-700">Quyền</label>
                <select v-model="editedUser.roleName" class="w-full mt-1 p-2 border border-gray-300 rounded">
                <option disabled value="">-- Chọn quyền --</option>
                <option>Nhân viên</option>
                <option>Quản trị viên</option>
              </select>
              <p v-if="errors.roleName" class="text-red-500 text-sm mt-1">{{ errors.roleName }}</p>
          </div>
          <div class="mb-4">
            <label class="block text-gray-700">Hoạt động</label>
            <select v-model="editedUser.isActive" class="w-full mt-1 p-2 border border-gray-300 rounded">
              <option :value="true">Đang hoạt động</option>
              <option :value="false">Ngưng hoạt động</option>
            </select>
          </div>
          <div class="flex justify-end">
            <button type="button" @click="resetForm" class="mr-2 px-4 py-2 rounded bg-gray-300 hover:bg-gray-400">
              Đặt lại
            </button>
            <button type="button" @click="showPopup = false" class="mr-2 px-4 py-2 rounded bg-gray-300 hover:bg-gray-400">
              Hủy
            </button>
            <button type="submit" class="bg-[#2292A7] text-white px-4 py-2 rounded hover:bg-[#1b7e91]">
              Lưu
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const users = ref([
  {
    id: 1,
    fullName: 'Nguyễn Văn A',
    email: 'vana@example.com',
    phoneNumber: '0909123456',
    roleName: 'Quản trị viên',
    registrationDate: '2023-12-01',
    isActive: true,
  },
  {
    id: 2,
    fullName: 'Trần Thị B',
    email: 'tranb@example.com',
    phoneNumber: '0909876543',
    roleName: 'Lễ tân',
    registrationDate: '2024-01-15',
    isActive: false,
  },
  {
    id: 3,
    fullName: 'Lê Minh C',
    email: 'minhc@example.com',
    phoneNumber: '0909777888',
    roleName: 'Nhân viên',
    registrationDate: '2024-03-22',
    isActive: true,
  }
]);

const showPopup = ref(false);
const isEditMode = ref(false);
const editedUser = ref({});
const errors = ref({});

// Mở form sửa
function openEditPopup(user) {
  isEditMode.value = true;
  editedUser.value = { ...user };
  errors.value = {};
  showPopup.value = true;
}

// Mở form thêm mới
function openAddPopup() {
  isEditMode.value = false;
  editedUser.value = {
    id: Date.now(),
    fullName: '',
    email: '',
    phoneNumber: '',
    roleName: '',
    registrationDate: new Date().toISOString().split('T')[0],
    isActive: true,
  };
  errors.value = {};
  showPopup.value = true;
}

// Reset form
function resetForm() {
  if (isEditMode.value) {
    openEditPopup(users.value.find(u => u.id === editedUser.value.id));
  } else {
    openAddPopup();
  }
}

// Lưu dữ liệu (thêm/sửa)
function saveChanges() {
  if (!validateForm()) return;

  // ✅ Xác nhận người dùng
  const confirmed = window.confirm("Bạn có chắc chắn muốn lưu thông tin này?");
  if (!confirmed) return;

  if (isEditMode.value) {
    const index = users.value.findIndex(u => u.id === editedUser.value.id);
    if (index !== -1) {
      users.value[index] = { ...editedUser.value };
    }
  } else {
    users.value.push({ ...editedUser.value });
  }
  showPopup.value = false;
}


// Validate email và số điện thoại
function validateForm() {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  const phoneRegex = /^0\d{9}$/;

  errors.value = {};

  if (!editedUser.value.email || !emailRegex.test(editedUser.value.email)) {
    errors.value.email = 'Email không hợp lệ.';
  }

  if (!editedUser.value.phoneNumber || !phoneRegex.test(editedUser.value.phoneNumber)) {
    errors.value.phoneNumber = 'Số điện thoại phải bắt đầu bằng 0 và có 10 chữ số.';
  }

  if (!editedUser.value.roleName) {
    errors.value.roleName = 'Vui lòng chọn quyền.';
  }

  return Object.keys(errors.value).length === 0;
}

// Trạng thái hoạt động
function getStatusClass(isActive) {
  return isActive
    ? 'px-2 inline-flex text-xs font-semibold rounded-full bg-green-100 text-green-800'
    : 'px-2 inline-flex text-xs font-semibold rounded-full bg-gray-200 text-gray-800';
}
</script>


<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');

.font-inter {
  font-family: 'Inter', sans-serif;
}

th, td {
  padding: 12px;
  word-wrap: break-word;
  white-space: normal;
}
</style>
