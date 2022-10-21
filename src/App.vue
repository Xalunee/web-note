<template>
  <div class="container vh-100 vw-100 text-center" v-show="step === 0">
    <h1>Заметки</h1>
  </div>
  <div class="container d-flex justify-content-center align-items-center vh-100 vw-100">
    <form @submit.prevent="autorizationUser" novalidate  v-show="step === 1" class="border p-5 rounded">
      <div class="step">
        <div class="mb-3">
          <label for="name" class="form-label">Имя</label>
          <input v-model="state.formAutoriz.name" type="text" class="form-control" id="name">
        </div>
        <div class="mb-3">
          <label for="password" class="form-label">Пароль</label>
          <input v-model="state.formAutoriz.password" type="password" class="form-control" id="password">
        </div>
        <button type="submit" class="btn btn-primary mb-1">Войти</button>
        <p>Нет аккаунта? <span @click="register" role="button" class="text-primary">Зарегистрироваться</span></p>
      </div>
    </form>

    <form @submit.prevent="registrationUser" novalidate v-show="step === 2" class="border p-5 rounded">
      <div class="step">
        <div class="mb-3">
          <label for="name" class="form-label">Имя</label>
          <input v-model="state.formReg.name" type="text" class="form-control" id="name">
        </div>
        <div class="mb-3">
          <label for="email" class="form-label">Почта</label>
          <input v-model="state.formReg.email" type="email" class="form-control" id="email">
        </div>
        <div class="mb-3">
          <label for="password" class="form-label">Пароль</label>
          <input v-model="state.formReg.password" type="password" class="form-control" id="password">
        </div>
        <div class="mb-3">
          <label for="confirmPassword" class="form-label">Повторить пароль</label>
          <input v-model="state.formReg.confirmPassword" type="password" class="form-control" id="confirmPassword">
        </div>
        <button type="submit" class="btn btn-primary mb-1">Зарегистрироваться</button>
        <p>У меня уже есть аккаунт. <span @click="autorization" role="button" class="text-primary">Войти</span></p>
      </div>
    </form>
  </div>
</template>

<script>
import { useVuelidate } from '@vuelidate/core'
import { required, email, minLength, maxLength, sameAs } from '@vuelidate/validators'
import { reactive, computed } from 'vue'
import axios from 'axios'

export default {
  setup() {
    const state = reactive({
      formAutoriz: {
        name: '',
        password: ''
      },
      formReg: {
        name: '',
        email: '',
        password: '',
        confirmPassword: ''
      }
    })

    const rules = computed(() => {
      return {
        formAutoriz: {
          name: { required },
          password: { required }
        },
        formReg: {
          name: { required, maxLength: maxLength(32) },
          email: { required, email },
          password: { required, minLength: minLength(6), maxLength: maxLength(32) },
          confirmPassword: { required, sameAs: sameAs(state.formReg.password) }
        }
      }
    })
    
    const v$ = useVuelidate(rules, state)

    return {
      state,
      v$,
    }
  },
  data() {
    return {
      step: 1,
    }
  },
  mounted() {
    if (localStorage.accessToken) {
      this.step = 0;
      axios.post('https://test-api.misaka.net.ru/api/Account/login', {
        "username": localStorage.userName,
        "password": localStorage.userPassword
      })
      .then(function(response) {
        console.log('Ответ сервера успешно получен!');
        localStorage.accessToken = response.data.accessToken;
        localStorage.refreshToken = response.data.refreshToken;
      })
      .catch(() => {
        console.log('Пользователь не найден');
      })
      setInterval(() => {
        axios.post('https://test-api.misaka.net.ru/api/Account/refresh-token', {
          "refreshToken": localStorage.refreshToken
          })
          .then(function(response) {
            console.log('Загрузка токенов в localStorage');
            localStorage.accessToken = response.data.accessToken;
            localStorage.refreshToken = response.data.refreshToken;
          })
          .catch(() => {
            console.log('Токен не загрузился');
          })
      }, 900000);

      axios.get('https://test-api.misaka.net.ru/api/Account/user', {
        headers: {
          Authorization: `Bearer ${localStorage.accessToken}`,
        }
      })
      .then(function(response) {
        console.log(response.data.email)
        console.log(response.data.id)
        console.log(response.data.username)
      })
      .catch(() => {
        console.log('Невозможно загрузить информацию о пользователе');
      })
    }
  },
  methods : {
    register() {
      if (this.step === 1) {
        this.step++
      }
    },
    autorization() {
      if (this.step === 2) {
        this.step--
      }
    },
    autorizationUser() {
      this.v$.$validate()
      if (!this.v$.formAutoriz.$error) {
        const user = {'name': this.state.formAutoriz.name, 'password': this.state.formAutoriz.password}
        axios.post('https://test-api.misaka.net.ru/api/Account/login', {
          "username": this.state.formAutoriz.name,
          "password": this.state.formAutoriz.password
          })
          .then(function(response) {
            console.log('Ответ сервера успешно получен!');
            localStorage.accessToken = response.data.accessToken;
            localStorage.refreshToken = response.data.refreshToken;
            localStorage.userName = user.name;
            localStorage.userPassword = user.password;
            location.reload();
            this.step = 0;
          })
          .catch(() => {
            console.log('Пользователь не найден');
          })
      } else {
        console.log('Авторизация прошла неудачно')
      }
    },
    registrationUser() {
      this.v$.$validate()
      if (!this.v$.formReg.$error) {
        const user = {'name': this.state.formReg.name, 'password': this.state.formReg.password};
        axios.post('https://test-api.misaka.net.ru/api/Account/register', {
          "username": this.state.formReg.name,
          "email": this.state.formReg.email,
          "password": this.state.formReg.password
          })
          .then(function(response) {
            console.log('Ответ с сервера успешно получен')
            localStorage.accessToken = response.data.accessToken;
            localStorage.refreshToken = response.data.refreshToken;
            localStorage.userName = user.name;
            localStorage.userPassword = user.password;
            location.reload();
            this.step = 0;
          })
          .catch(() => {
            console.log('Пользователь зарегистрирован');
          })
      } else {
        console.log('Регистрация прошла неудачно')
      }
    }
  }
}
</script>

<style>

</style>
