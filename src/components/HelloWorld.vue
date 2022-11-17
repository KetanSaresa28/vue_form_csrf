<script>
import axios from 'axios';
axios.defaults.baseURL = 'https://csrf-token-api.herokuapp.com/api';

export default {
    data() {
      return {
        name: "",
        email: "",
        password: ""
      };
    },
    methods: {
      submitForm: function () {
        console.log(this.name)
        console.log(this.email)
        console.log(this.password)

        const data = {
          username: this.name.trim(),
          email: this.email.trim(),
          password: this.password
        };

        this.loading = true;
        axios.post('/submit', data, { withCredentials: true })
          .then(async (res) => {
            if (res.data.success) {
              const user = res.data.data;
              user.isProducer = true;
              console.log(user);
              alert('CSRF token validated successfully !')
            } else {
              console.log(res.data.message);
            }
          })
          .catch(err => {
            alert('Error, Invalid CSRF token !')
            console.log(err);
          })
          .then(() => { this.loading = false })
      },
      getCSRFToken() {
        axios.get('/csrf/token', { withCredentials: true })
          .then(async (res) => {
            if (res.data.success) {
              const csrfToken = res.data.data.csrfToken;
              axios.defaults.headers.common['X-CSRF-TOKEN'] = csrfToken;
            } else {
              console.log(res.data.message);
            }
          })
          .catch(err => {
            console.log(err);
          })
      }
    },
    beforeMount() {
      this.getCSRFToken()
    }
  };
</script>

<template>
    <div>
    <form @submit.prevent="submitForm">
      <span>Full Name</span><br>
      <input 
        v-model="name"
        type="text"
        placeholder="Enter your name" 
      /><br>
      <span>Email</span><br>
      <input 
        v-model="email"
        type="email"
        placeholder="Enter your email" 
      /><br>
      <span>Password</span><br>
      <input 
        v-model="password"
        type="password"
        placeholder="Enter your password" 
      /><br>
      <input 
        class="submit" 
        type="submit" 
        value="Submit"
      >
    </form>
    
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
