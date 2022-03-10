<template>
  <div class="tests">
    <div class="admin-content">
      <h1>Пользователи:</h1>
      <div v-for="(user, index) in users" :key="index">
        <ButtonMini @click="userData.id=user.id; change">{{ user.email }}</ButtonMini>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import {
  defineComponent, ref, useContext, useRouter, useStore,
} from '@nuxtjs/composition-api'

export default defineComponent({
  middleware: ['auth', 'isAdmin'],

  setup() {
    const router = useRouter()
    const { $axios } = useContext()
    const store = useStore()
    const error = ref(null as String | null)

    const userData = ref({
      id: 0
    } as any)

    const validate = () => {
      error.value = null

      if (
        !userData.value.email
      )
        return error.value = 'Заполните все поля!'

      if (userData.value.email.indexOf('@') == -1)
        return error.value = 'Неверный формат e-mail!'

      return true
    }

    const users = ref([])

    $axios.get('/v1/users').then((data) => users.value = data.data)

    const change = async () => {
      if (validate() !== true) return

      const response = await store.dispatch('auth/newAdmin', userData.id)
      if (response)
        router.push({ name: 'admin/index' })
    }
    return { error, userData, change, users }
  },
  
})
</script>
