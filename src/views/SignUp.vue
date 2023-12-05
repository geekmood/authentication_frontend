<!-- <template>
  <div class="sign-up-container">
    <h2 class="header-container">Sign Up</h2>

    <a-form @submit.prevent="submitForm">
      <a-form-item label="Email" class="lable_l">
        <a-input v-model:value="userData.email" type="email" required />
      </a-form-item>

      <template v-if="isEmailValid">
        <a-form-item label="Full Name" class="lable_l">
          <a-input v-model:value="userData.full_name" required />
        </a-form-item>

        <a-form-item label="Phone" class="lable_l">
          <a-input v-model:value="userData.phone" type="text" @input="acceptNumber" required />
        </a-form-item>

        <a-form-item label="Address" class="lable_l">
          <a-input v-model:value="userData.address" required />
        </a-form-item>

        <a-form-item label="Password" :validateStatus="passwordValidateStatus" :help="passwordValidateHelp">
          <a-input v-model:value="userData.password" type="password" @input="validatePassword" required />
        </a-form-item>

        <template v-if="isPasswordValid">
          <a-form-item label="Password Confirmation" :validateStatus="confirmPasswordValidateStatus"
            :help="confirmPasswordValidateHelp" class="form-item">
            <a-input v-model:value="confirmPassword" type="password" @input="validateConfirmationPassword" required />
          </a-form-item>
        </template>

        <a-form-item class="button-container">
          <a-button type="primary" html-type="submit">Sign Up</a-button>
        </a-form-item>
      </template>
    </a-form>
  </div>
</template> -->

<template>
  <div class="sign-up-container">
    <h2 class="header-container">Sign Up</h2>

    <a-form @submit.prevent="submitForm">

      <div class="form-group">
        <label for="email" class="lable_l">Email</label>
        <a-input v-model:value="userData.email" id="email" class="form-control" required />
      </div>
      <template v-if="isEmailValid">

        <div class="form-group">
          <label for="full-name" class="lable_l">Full Name</label>
          <a-input v-model:value="userData.full_name" id="full-name" class="form-control" required />
        </div>

        <div class="form-group">
          <label for="phone" class="lable_l">Phone</label>
          <a-input v-model:value="userData.phone" type="tel" @input="acceptNumber" id="phone" class="form-control"
            required />
        </div>

        <div class="form-group">
          <label for="address" class="lable_l">Address</label>
          <a-input v-model:value="userData.address" id="address" class="form-control" required />
        </div>

        <div class="form-group">
          <label for="password" class="lable_l">Password</label>
          <a-form-item :validateStatus="passwordValidateStatus" :help="passwordValidateHelp">
            <a-input v-model:value="userData.password" type="password" @input="validatePassword" id="password"
              class="form-control" required />
          </a-form-item>
        </div>

        <template v-if="isPasswordValid">
          <div class="form-group">
            <label for="confirm-password" class="lable_l">Confirm Password</label>
            <a-form-item :validateStatus="confirmPasswordValidateStatus" :help="confirmPasswordValidateHelp">
              <a-input v-model:value="confirmPassword" type="password" @input="validateConfirmationPassword"
                id="confirm-password" class="form-control" required />
            </a-form-item>
          </div>
        </template>

        <a-form-item class="button-container">
          <a-button type="primary" html-type="submit">Sign Up</a-button>
        </a-form-item>
      </template>
    </a-form>
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
      userData: {
        full_name: '',
        email: '',
        phone: '',
        address: '',
        password: ''
      },
      passwordValidateStatus: '',
      passwordValidateHelp: '',
      confirmPassword: '',
      confirmPasswordValidateStatus: '',
      confirmPasswordValidateHelp: '',
      isPasswordValid: false,
    };
  },
  computed: {
    isEmailValid() {
      const value = /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/;
      return value.test(this.userData.email);
    }
  },
  methods: {

    acceptNumber() {
      var numericValue = this.userData.phone.replace(/\D/g, '');
      this.userData.phone = numericValue;
    },

    validateConfirmationPassword() {
      const originalPassword = this.userData.password;
      const confirmationPassword = this.confirmPassword;

      if (confirmationPassword.length === 0) {
        this.confirmPasswordValidateStatus = '';
        this.confirmPasswordValidateHelp = '';
        return;
      }

      if (originalPassword === confirmationPassword) {
        this.confirmPasswordValidateStatus = 'success';
        this.confirmPasswordValidateHelp = '';
      } else {
        this.confirmPasswordValidateStatus = 'error';
        this.confirmPasswordValidateHelp = 'Passwords do not match.';
      }
    },

    validatePassword() {
      const password = this.userData.password;
      const isLengthValid = password.length >= 8;
      const alphabet = /[A-Z]/;
      const numeric = /[0-9]/;
      const format = /[!@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?]+/;

      const validationMessages = [];

      if (password.length === 0) {
        this.passwordValidateStatus = '';
        this.passwordValidateHelp = '';
        return;
      }

      if (!isLengthValid) {
        validationMessages.push('Password must be at least 8 characters.');
      }

      if (!alphabet.test(password)) {
        validationMessages.push('Must contain at least one uppercase letter.');
      }

      if (!numeric.test(password)) {
        validationMessages.push('Must contain at least one numeric digit (0-9).');
      }

      if (!format.test(password)) {
        validationMessages.push('Must contain at least one special character (!@#..).');
      }

      this.passwordValidateStatus = validationMessages.length === 0 ? 'success' : 'error';
      this.passwordValidateHelp = validationMessages.join(' ');

      this.isPasswordValid = (this.passwordValidateStatus === 'success');
    },

    async submitForm() {
      try {
        this.validatePassword();
        this.validateConfirmationPassword();
        if (this.passwordValidateStatus === 'success' && this.confirmPasswordValidateStatus === 'success') {
          const response = await axios.post(`http://localhost:3000/users`, {
            user: this.userData
          });
          console.log('API Response:', response.data);

          // Clear form fields after successful submission
          this.userData = {
            full_name: '',
            email: '',
            phone: '',
            address: '',
            password: ''
          };
          this.confirmPassword = '';
        }
      } catch (error) {
        console.error('Error submitting form:', error);
      }
    }
  }
};
</script>

<style scoped>
.sign-up-container {
  min-width: 700px;
  margin: auto;
  padding: 20px;
  border: 1px solid #d9d9d9;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  background-color: white;
  color: black;
}

a-form-item {
  margin-bottom: 15px;
}

a-button {
  width: 100%;
}

a-form-item label {
  color: white;
}

.lable_l {
  margin-bottom: 5px;
  margin-top: 5px;
}

.button-container {
  display: flex;
  justify-content: center;
  width: 100%;
  margin-top: 15px;
}

.header-container {
  display: flex;
  justify-content: center;
  width: 100%;
  margin-bottom: 15px;
}
</style>
