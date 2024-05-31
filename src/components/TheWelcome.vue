
<template>
  <div class="user_wrapper">
    <div v-for="user in users" :key="user.id">
      <UserItem @delete="deleteUser(user.id)">
        <template #icon>
          <CommunityIcon />
        </template>
        <template #heading>{{ user.name }}</template>
        <a href="#">{{ user.email }}</a>
        <p>{{ user.message }}</p>
      </UserItem>
    </div>
  </div>
</template>

<script setup>


import { ref, onMounted } from 'vue';
import axios from 'axios';
import UserItem from './UserItem.vue'
import CommunityIcon from './icons/IconCommunity.vue'

const users = ref([]);

// Fetch users using Axios interceptor for error handling
const fetchUsers = () => {
  axios.get('http://localhost:3000/users')
    .then(response => {
      users.value = response.data;
    })
    .catch(error => {
      console.error('Error fetching users:', error);
    });
};

const deleteUser = (id) => {
  axios.delete(`http://localhost:3000/users/${id}`)
    .then(() => {
      users.value = users.value.filter(user => user.id !== id);
    })
    .catch(error => {
      console.error('Error deleting user:', error);
    });
};

onMounted(fetchUsers);

</script>

<style scoped>
.user_wrapper {
  padding: 0 10%;
  max-height: 500px;
  overflow-y: auto;
}
</style>
