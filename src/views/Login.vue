<template>
  <div class="login-page">
    <form class="login-form">
      <h2 class="login-title">Dashy</h2>
      <Input v-model="username" label="Username" class="login-field username" type="text" />
      <Input v-model="password" label="Password" class="login-field password" type="password" />
      <Button class="login-button" :click="submitLogin">Login</Button>
      <transition name="bounce">
      <p :class="`login-error-message ${status}`" v-show="message">{{ message }}</p>
      </transition>
    </form>
  </div>
</template>

<script>
import router from '@/router';
import Button from '@/components/FormElements/Button';
import Input from '@/components/FormElements/Input';
import Defaults, { localStorageKeys } from '@/utils/defaults';
import { checkCredentials, login } from '@/utils/Auth';

export default {
  name: 'login',
  props: {
    appConfig: Object,
  },
  data() {
    return {
      username: '',
      password: '',
      message: '',
      status: 'waiting', // wating, error, success
    };
  },
  components: {
    Button,
    Input,
  },
  methods: {
    submitLogin() {
      const response = checkCredentials(this.username, this.password, this.appConfig.auth || []);
      this.message = response.msg; // Show error or success message to the user
      this.status = response.correct ? 'success' : 'error';
      if (response.correct) { // Yay, credentials were correct :)
        login(this.username, this.password); // Login, to set the cookie
        setTimeout(() => { // Wait a short while, then redirect back home
          router.push({ path: '/' });
        }, 250);
      }
    },
    setTheme() {
      const theme = localStorage[localStorageKeys.THEME] || Defaults.theme;
      document.getElementsByTagName('html')[0].setAttribute('data-theme', theme);
    },
  },
  created() {
    this.setTheme();
  },
};

</script>

<style lang="scss">

.login-page {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 800px;

  .login-form {
    background: var(--login-form-background);
    color: var(--login-form-color);
    border: 1px solid var(--login-form-color);
    border-radius: var(--curve-factor);
    padding: 2rem;
    margin: 2rem auto;
    display: flex;
    flex-direction: column;

    h2.login-title {
      font-size: 3rem;
      margin: 0 0 1rem 0;
      text-align: center;
    }

    .login-field input, Button.login-button {
      width: 18rem;
      margin: 0.5rem auto;
      font-size: 1.4rem;
      padding: 0.5rem 1rem;
    }

    .login-field input {
      color: var(--login-form-color);
      border-color: var(--login-form-background-secondary);
      background: var(--login-form-background-secondary);
      &:focus {

      }
    }
    Button.login-button {
      background: var(--login-form-background-secondary);
      border-color: var(--login-form-background-secondary);
      &:hover {
        border-color: var(--login-form-color);
        color: var(--login-form-background-secondary);
        background: var(--login-form-color);
      }
      &:active, &:focus {
        box-shadow: 1px 1px 6px var(--login-form-color);
      }
    }
    p.login-error-message {
      font-size: 1rem;
      text-align: center;
      &.waiting { color: var(--login-form-color); }
      &.success { color: var(--success); }
      &.error { color: var(--warning); }
    }
  }
}

.bounce-enter-active { animation: bounce-in 0.25s; }
.bounce-leave-active { animation: bounce-in 0.25s reverse; }
@keyframes bounce-in {
  0% { transform: scale(0); }
  50% { transform: scale(1.25); }
  100% { transform: scale(1); }
}

</style>