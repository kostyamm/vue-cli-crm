<template>
  <form class='card auth-card' @submit.prevent='submitHandler'>
    <div class='card-content'>
      <span class='card-title'>Домашняя бухгалтерия</span>
      <div class='input-field'>
        <input
            id='email'
            type='text'
            v-model.trim='email'
            :class='{ "invalid" : ($v.email.$dirty && !$v.email.required) || ($v.email.$dirty && !$v.email.email) }'
        >
        <label for='email'>Email</label>
        <small
            v-if='$v.email.$dirty && !$v.email.required'
            class='helper-text invalid'
        >Введите Email</small>
        <small
            v-else-if='$v.email.$dirty && !$v.email.email'
            class='helper-text invalid'
        >Введите корректный Email</small>
      </div>
      <div class='input-field'>
        <input
            id='password'
            type='password'
            v-model.trim='password'
            :class='{ "invalid" : ($v.password.$dirty && !$v.password.required) || ($v.password.$dirty && !$v.password.minLength) }'
        >
        <label for='password'>Пароль</label>
        <small
            v-if='$v.password.$dirty && !$v.password.required'
            class='helper-text invalid'
        >Введите пароль</small>
        <small
            v-else-if='$v.password.$dirty && !$v.password.minLength'
            class='helper-text invalid'
        >Пароль должен быть не меньше
          {{ $v.password.$params.minLength.min }}
          символов. <br> Сейчас он {{ password.length }}</small>
      </div>
    </div>
    <div class='card-action'>
      <div>
        <button
            class='btn waves-effect waves-light auth-submit'
            type='submit'
            v-tooltip='"Вход в кабинет"'
        >
          Войти
          <i class='material-icons right'>send</i>
        </button>
      </div>

      <p class='center'>
        Нет аккаунта?
        <router-link to='/register'>Зарегистрироваться</router-link>
      </p>
    </div>
  </form>
</template>

<script>
import { email, required, minLength } from 'vuelidate/lib/validators'
import messages from '@/utils/messages'
export default {
  name: 'Login',
  data: () => ({
    email: '',
    password: ''
  }),
  validations: {
    email: { email, required },
    password: { required, minLength: minLength(6) }
  },
  methods: {
    async submitHandler () {
      if (this.$v.$invalid) {
        this.$v.$touch()
        return
      }
      const formData = {
        email: this.email,
        password: this.password
      }
      try {
        await this.$store.dispatch('login', formData)
        await this.$router.push('/')
      } catch (e) {}
    }
  },
  mounted () {
    if (messages[this.$route.query.message]) {
      this.$message(messages[this.$route.query.message])
      this.$router.push({query: {}})
    }
  }
}
</script>
