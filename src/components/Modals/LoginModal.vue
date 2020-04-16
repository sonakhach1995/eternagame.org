<template>
  <b-modal
    modal-class="login_modal"
    id="modal-login"
    ref="modal"
    body-class="py-0"
    header-border-variant="primary"
    hide-footer
  >
    <template #modal-title>
      <b>{{ $t('login-row:main-action').toUpperCase() }}</b>
    </template>
    <transition name="fade">
      <b-alert class="mt-3" show variant="danger" v-if="errorMessage">
        {{ errorMessage }}
      </b-alert>
    </transition>
    <b-form @submit.prevent="login" class="login_modal_content">
      <div class="custom-input-group">
        <b-input :placeholder="$t('login-modal:username')" v-model="form.username" required />
        <span class="input-group-append">
          <img src="@/assets/front-page/img/user.svg" />
        </span>
      </div>
      <div class="custom-input-group">
        <b-input
          type="password"
          :placeholder="$t('login-modal:password')"
          v-model="form.password"
          required
        />
        <span class="input-group-append">
          <img src="@/assets/front-page/img/lock.svg" />
        </span>
      </div>

      <p
        class="mt-0"
        @click="$bvModal.hide('modal-login')"
        v-b-modal.modal-reset-password
        style="text-align:end; text-decoration:underline;margin-top:"
      >
        {{ $t('login-sub:main-action') }}
      </p>
      <b-button type="submit" variant="primary" class="submit-button">{{
        $t('login-modal:main-action').toUpperCase()
      }}</b-button>

      <p style="text-align:center">
        {{ $t('login-modal:register-pre-text') }}
        <span
          style="text-decoration:underline"
          @click="$bvModal.hide('modal-login')"
          v-b-modal.modal-register
        >
          {{ $t('login-modal:register-action') }}
        </span>
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
  .submit-button {
    margin-top: 1.5rem;
  }

  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.15s;
  }
  .fade-enter,
  .fade-leave-to {
    opacity: 0;
  }
</style>
