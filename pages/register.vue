<template>
  <div class="register">
    <div class="register-content">
      <h1>Регистрация</h1>
      <a href="/auth">Авторизация</a>
      <p v-if="error" class="error">{{ error }}</p>
      <div class="register-form">
        <input type="email" placeholder="E-mail" v-model="registerData.email">
        <input type="password" placeholder="Пароль" v-model="registerData.password">
        <input type="password" placeholder="Повторите пароль" v-model="registerData.repeatPassword">
        <div class="isAdminContainer">
          <p>Я учитель</p>
          <input type="checkbox" v-model="registerData.isAdmin">
        </div>
        <ButtonMini @click="register">Зарегистрироваться</ButtonMini>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import {
  defineComponent, ref, useRouter, useStore,
} from '@nuxtjs/composition-api'

export default defineComponent({
  setup() {
    const store = useStore()
    const router = useRouter()

    const error = ref(null as String | null)
    const registerData = ref({
      email: '',
      password: '',
      repeatPassword: '',
      isAdmin: 0
    })

    const validate = () => {
      error.value = null

      if (
        !registerData.value.email ||
        !registerData.value.password ||
        !registerData.value.repeatPassword
      )
        return error.value = 'Заполните все поля!'

      if (registerData.value.email.indexOf('@') == -1)
        return error.value = 'Неверный формат e-mail!'

      if (registerData.value.password != registerData.value.repeatPassword)
        return error.value = 'Пароли не совпадают!'

      return true
    }

    const register = async () => {
      if (validate() !== true) return

      const response = await store.dispatch('auth/register', registerData.value)
      if (response)
        router.push({ name: 'index' })
    }

    return { error, registerData, register }
  },
})
</script>

<style scoped>
  .isAdminContainer{
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
  }
  .isAdminContainer p{
    color: #2F80ED;
    font-size: 14px;
    margin-top: 20px;
    width: 20%;
  }
  .isAdminContainer input{
    width: 20%;
  }
</style>