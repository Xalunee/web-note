<template>
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
        console.log('Авторизация прошла успешно')
        console.log(this.state.formAutoriz.name)
        console.log(this.state.formAutoriz.password)
      } else {
        console.log('Авторизация прошла неудачно')
      }
    },
    registrationUser() {
      this.v$.$validate()
      if (!this.v$.formReg.$error) {
        console.log('Регистрация прошла успешно')
        console.log(this.state.formReg.name)
        console.log(this.state.formReg.email)
        console.log(this.state.formReg.password)
        console.log(this.state.formReg.confirmPassword)
      } else {
        console.log('Регистрация прошла неудачно')
      }
    }
  }
}
</script>

<style>

</style>
