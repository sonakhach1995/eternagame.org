<template>
  <b-modal modal-class="login_modal"
    id="modal-login"
    ref="modal"
    body-class="py-0"
    header-border-variant="primary"
    hide-footer
  >
    <template #modal-title>
      <b>{{ $t('login-row:main-action') }}</b>
    </template>
    <transition name="fade">
      <b-alert class="mt-3" show variant="danger" v-if="errorMessage">
        {{ errorMessage }}
      </b-alert>
    </transition>
    <b-form @submit.prevent="login" class="login_modal_content">
      <div class="input_bg">
      <b-input placeholder="username" v-model="form.username" required />
        <span>
          <img src="@/assets/front-page/img/noun_User.svg">
        </span>
      </div>
      <div class="input_bg">
      <b-input type="password" placeholder="password" v-model="form.password" required />
        <span class="lock">
          <img src="@/assets/front-page/img/noun_Lock.svg">
        </span>
      </div>
      <p class="forgot-password mt-0" @click="$bvModal.hide('modal-login')"
         v-b-modal.modal-reset-password>
        {{ $t('login-sub:main-action') }}
      </p>
      <b-button type="submit" variant="primary" class="submit-button">{{
        $t('login-modal:main-action')
      }}</b-button>

      <p class="register_here" @click="$bvModal.hide('modal-login')"
        v-b-modal.modal-register >
        {{ $t('login-modal:register-action') }}
      </p>
    </b-form>
  </b-modal>
</template>

<script lang="ts">
  import { Component, Prop, Vue } from 'vue-property-decorator';
  import { BModal, BFormInput } from 'bootstrap-vue';
  import VueRecaptcha from 'vue-recaptcha';

  @Component({
    components: {
      VueRecaptcha,
    },
  })
  export default class RegisterModal extends Vue {
    form = {
      username: '',
      password: '',
    };

    errorMessage = '';

    $refs!: {
      modal: BModal;
      rePassword: BFormInput;
    };

    async login() {
      if (this.form.username && this.form.password) {
        const data = await this.$vxm.user.login({
          username: this.form.username,
          password: this.form.password,
        });
        if (data.success) {
          this.$router.push('/');
        } else {
          this.$vxm.user.showLoginFailedModal({ errorMessage: data.error });
        }
      }
    }
  }
</script>

<style scoped lang="scss">

  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.15s;
  }
  .fade-enter,
  .fade-leave-to {
    opacity: 0;
  }

</style>
