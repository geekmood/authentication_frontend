<template>
  <div>
    <div v-if="isLogedIn" class="container mt-5">
      <h1 class="text-success">Hurry...!!! Logged in successfully</h1>
      <p>Want to log out</p>
      <button class="btn btn-primary" @click="logout">Log Out</button>
    </div>

    <div v-if="!isLogedIn" class="signIn-container">
      <h2 class="header-container">Sign In</h2>
      <a-form @submit.prevent="submitForm">
        <div class="mb-3">
          <label for="email" class="form-label">Email</label>
          <a-input v-model:value="userData.email" type="email" class="form-control" required />
        </div>

        <div class="mb-3">
          <label for="password" class="form-label">Password</label>
          <a-input v-model:value="userData.password" type="password" class="form-control" required />
        </div>

        <a-form-item class="button-container">
          <a-button html-type="submit" class="btn btn-primary">Sign In</a-button>
        </a-form-item>
      </a-form>
    </div>
  </div>
</template>

<script>
import { Form, Input, Button } from 'ant-design-vue';
import axios from 'axios';

export default {
  components: {
    'a-form': Form,
    'a-form-item': Form.Item,
    'a-input': Input,
    'a-button': Button
  },
  data() {
    return {
      isLogedIn: false,
      userData: {
        email: '',
        password: ''
      }
    };
  },
  methods: {
    logout() {
      this.isLogedIn = false;
    },
    async submitForm() {
      try {
        const response = await axios.post('http://localhost:3000/login', this.userData);
        console.log('API Response:', response.data);
        this.isLogedIn = true;
        // Reset form fields
        this.userData = {
          email: '',
          password: ''
        };
      } catch (error) {
        console.error('Error submitting form:', error);
      }
    }
  }
};
</script>

<style scoped>
/* Add your CSS styles here */
.loggedIn-container {
  background-color: #eaff8f;
  padding: 20px;
  border: 1px solid #b7eb8f;
}

.signIn-container {
  background-color: white;
  padding: 20px;
  border: 1px solid #d9d9d9;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  color: black;
}

.button-container {
  display: flex;
  justify-content: center;
  width: 100%;
}

.header-container {
  display: flex;
  justify-content: center;
  width: 100%;
  margin-bottom: 15px;
}

/* Add more styles as needed */
</style>




