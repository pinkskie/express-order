<template>
  <div class="register">
    <VHeader backTo="/" className="active"/>
    <div class="email" >
      <div v-if="step === 1">
        <h3 class="email__title">Регистрация</h3>
        <span class="email__info">Введите ваш почтовый адрес</span>
        <Input 
          @keyup.enter="handleClick" 
          type="text" 
          placeholder="Введите почтовый адрес" 
          label="e-mail" 
          v-model:input="email"
          ref="email"
        />
        <span v-show="errorMsg" class="error">Некорректный email</span>
        <span class="email__description">Нажимая  “Далее”, вы принимаете <br/> <router-link to="#">условия публичной оферты</router-link> </span>
      </div>
  
      <transition name="fade">
        <div v-if="step === 2">
          <h3 class="email__title">Введите пароль</h3>
          <span class="email__info">Пароль должен состоять минимум из <br/> 9 символов</span>
          <Input 
            @keyup.enter="handleClick" 
            type="password" 
            placeholder="Введите пароль" 
            label="Пароль" 
            ref="password"
            v-model:input="password"
          />
          <span v-show="errorMsg" class="error">Пароль должен содержать минимум 9 символов</span>
          <span class="email__description">Нажимая  “Далее”, вы принимаете <br/> <router-link to="#">условия публичной оферты</router-link></span>
        </div>
      </transition>
      <p v-if="error.length" class="error-message">
        Произошла ошибка, пожалуйста повторите попытку
      </p>
    </div>
    <ButtonRed 
      :type="step == 2 ? 'submit' : 'button' " 
      className="fixed" 
      @click="handleClick" 
      :disabled="!email"
    >
      Далее
    </ButtonRed>  
  </div>
</template>

<script>
  import ButtonRed  from '../components/button-red.vue'
  import VHeader from '../components/header.vue'
  import Input from '../components/my-input.vue'
  import api from '../services/api'
  export default {
    data() {
      return {
        email: '',
        password: '',
        step: 1,
        error: '',
        errorMsg: false,
      }
    },
    components: { VHeader, Input, ButtonRed },
    methods: {
      handleClick() {
        if (this.step === 1) {
          if (!this.errorMsg) {
            this.step++
            setTimeout(() => {
              this.$refs.password.$el.focus();
            }, 0)
          }
        } else if (this.step === 2 ) {
          if (this.errorMsg) {
            return false
          }
          api.register({email: this.email, password: this.password}).then(data => {
            if (data.message === 'User created successfully') {
              localStorage.setItem('authorized', true)
              this.$router.push('/login')
            } else {
              this.error = data.message
            }
          }).catch(error => {
            this.error = error.message
          })
        }
      },
      focusInput() {
        this.$refs.email.$el.focus();
      },
    },
    watch: {
      email() {
        // eslint-disable-next-line
        const req = /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/;
        if (req.test(this.email)) {
          this.errorMsg = false;
          return false
        } else {
          this.errorMsg = true;
          return true
        }
      },
      password() {
        this.errorMsg = this.password.length < 9
      }
    },
    mounted() {
      this.focusInput();
    },
  }
</script>

<style scoped>
  .register {
    background-color: white;
    width: 100%;
    height: 100vh;
    display: flex;
    flex-direction: column;
    align-content: center;
    position: relative;
  }
  .email {
    padding: 0 1.25rem;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    height: 100%;
  }
  .email__title {
    color: var(--text-primary);
    font-size: 1.25rem;
    margin-bottom: .75rem;
    text-align: center;
  }
  .email__info {
    font-size: 1rem;
    color: var(--text-grey);
    margin-bottom: 1.25rem;
    text-align: center;
    display: block;
  }
  .email__description {
    font-size: 0.75em;
    color: var(--text-grey);
    line-height: .9rem;
    margin-top: 1rem;
    display: block;
    text-align: center;
  }
  .email__description a {
    text-decoration: none;
    color: var(--text-grey);
    border-bottom: 1px solid var(--text-grey);
  }
  .error {
    color: red;
    font-size: .8rem;
  }
  .error-message {
    color: red;
    font-size: .8rem;
    text-align: center;
    margin-bottom: 2rem;
  }
  .active {
    align-self: flex-start;
    box-shadow: none;
    margin-bottom: 1.75rem;
  }
  .fixed {
    margin-bottom: 1.5rem;
  }
  .fade-enter-active,
  .fade-leave-active {
    transition: 0.5s ease;
  }

  .fade-enter-from,
  .fade-leave-to {
    opacity: 0;
  }
</style>