<template>
  <div class="container">
    <div class="login-brand">
      <div class="login-brand__content">
        <p class="login-brand__tag">VUE TEMPLATE</p>
        <h1>{{ $t(systemTitle) }}</h1>
        <p class="login-brand__desc">{{ $t('message.system.loginWelcome') }}</p>
        <ul class="login-brand__feature">
          <li>{{ $t('message.system.loginFeatureOne') }}</li>
          <li>{{ $t('message.system.loginFeatureTwo') }}</li>
          <li>{{ $t('message.system.loginFeatureThree') }}</li>
        </ul>
      </div>
    </div>
    <div class="login-panel-wrap">
      <div class="fixed-top-right">
        <select-lang />
      </div>
      <div class="login-panel">
        <div class="login-panel__header">
          <h2>{{ $t('message.system.loginTitle') }}</h2>
          <p>{{ $t('message.system.loginSubTitle') }}</p>
        </div>

        <div class="login-mode">
          <div
            class="login-mode__item"
            :class="{ 'is-active': loginMode === 'password' }"
            @click="loginMode = 'password'"
          >
            {{ $t('message.system.passwordLogin') }}
          </div>
          <div
            class="login-mode__item"
            :class="{ 'is-active': loginMode === 'qrcode' }"
            @click="loginMode = 'qrcode'"
          >
            {{ $t('message.system.qrLogin') }}
          </div>
        </div>

        <el-form v-if="loginMode === 'password'" class="form">
          <el-input
            size="large"
            v-model="form.name"
            :placeholder="$t('message.system.userName')"
            type="text"
            maxlength="50"
          >
            <template #prepend>
              <i class="sfont system-xingmingyonghumingnicheng"></i>
            </template>
          </el-input>
          <el-input
            size="large"
            v-model="form.password"
            :type="passwordType"
            :placeholder="$t('message.system.password')"
            name="password"
            maxlength="50"
          >
            <template #prepend>
              <i class="sfont system-mima"></i>
            </template>
            <template #append>
              <i class="sfont password-icon" :class="passwordType ? 'system-yanjing-guan': 'system-yanjing'" @click="passwordTypeChange"></i>
            </template>
          </el-input>
          <div class="captcha-row">
            <el-input
              size="large"
              v-model="form.captcha"
              :placeholder="$t('message.system.captchaPlaceholder')"
              maxlength="10"
            >
              <template #prepend>
                <i class="el-icon-picture"></i>
              </template>
            </el-input>
            <div class="captcha-box" @click="refreshCaptcha">
              <span>{{ fakeCaptcha }}</span>
              <small>{{ $t('message.common.refresh') }}</small>
            </div>
          </div>
          <el-button type="primary" @click="submit" class="submit-btn" size="large">{{ $t('message.system.login') }}</el-button>
        </el-form>

        <div v-else class="qrcode-box">
          <div class="qrcode-box__img">
            <div class="qrcode-grid"></div>
            <div class="qrcode-center-logo">V</div>
          </div>
          <h3>{{ $t('message.system.qrTipTitle') }}</h3>
          <p>{{ $t('message.system.qrTipDesc') }}</p>
          <el-button plain class="submit-btn" @click="mockQrLogin">{{ $t('message.system.mockScanLogin') }}</el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { systemTitle } from '@/config'
import { computed, defineComponent, reactive, ref } from 'vue'
import { useStore } from 'vuex'
import { useRouter, useRoute } from 'vue-router'
import { addRoutes } from '@/router'
import { ElMessage } from 'element-plus'
import selectLang from '@/layout/Header/functionList/word.vue'
export default defineComponent({
  components: {
    selectLang
  },
  setup() {
    const store = useStore()
    const router = useRouter()
    const route = useRoute()
    const loginMode = ref<'password' | 'qrcode'>('password')
    const form = reactive({
      name: 'admin',
      password: '123456',
      captcha: ''
    })
    const passwordType = ref('password')
    const captchaSeed = ref(Date.now())
    const captchaChars = '23456789ABCDEFGHJKLMNPQRSTUVWXYZ'
    const fakeCaptcha = computed(() => {
      let value = ''
      const seed = String(captchaSeed.value)
      for (let i = 0; i < 4; i++) {
        const index = (Number(seed[seed.length - 1 - i] || i) + i * 7) % captchaChars.length
        value += captchaChars[index]
      }
      return value
    })
    const passwordTypeChange = () => {
      passwordType.value === '' ? passwordType.value = 'password' : passwordType.value = ''
    }
    const refreshCaptcha = () => {
      captchaSeed.value = Date.now()
      form.captcha = ''
    }
    const checkForm = () => {
      return new Promise((resolve, reject) => {
        if (form.name === '') {
          ElMessage.warning({
            message: '用户名不能为空',
            type: 'warning'
          });
          return;
        }
        if (form.password === '') {
          ElMessage.warning({
            message: '密码不能为空',
            type: 'warning'
          })
          return;
        }
        if (form.captcha.trim().toUpperCase() !== fakeCaptcha.value) {
          ElMessage.warning({
            message: '验证码错误',
            type: 'warning'
          })
          refreshCaptcha()
          return;
        }
        resolve(true)
      })
    }
    const loginSuccess = () => {
      ElMessage.success({
        message: '登录成功',
        type: 'success',
        showClose: true,
        duration: 1000
      })
      addRoutes()
      router.push(route.query.redirect || '/')
    }
    const submit = () => {
      checkForm()
      .then(() => {
        let params = {
          name: form.name,
          password: form.password
        }
        store.dispatch('user/login', params)
        .then(() => {
          loginSuccess()
        })

      })
    }
    const mockQrLogin = () => {
      const params = {
        name: 'admin',
        password: '123456'
      }
      store.dispatch('user/login', params)
      .then(() => {
        loginSuccess()
      })
    }
    return {
      systemTitle,
      loginMode,
      form,
      passwordType,
      fakeCaptcha,
      passwordTypeChange,
      refreshCaptcha,
      submit,
      mockQrLogin
    }
  }
})
</script>

<style lang="scss" scoped>
  .container {
    display: flex;
    width: 100vw;
    height: 100vh;
    background: #f7f8fa;
  }
  .login-brand {
    flex: 1;
    display: flex;
    align-items: center;
    padding: 0 8vw;
    color: #1f2937;
    &__tag {
      display: inline-block;
      padding: 6px 12px;
      margin-bottom: 18px;
      border-radius: 999px;
      font-size: 12px;
      letter-spacing: 2px;
      background: #ffffff;
      border: 1px solid #e5e7eb;
      color: #6b7280;
    }
    h1 {
      margin: 0;
      font-size: 42px;
      line-height: 1.2;
      color: #111827;
    }
    &__desc {
      margin: 18px 0 28px;
      max-width: 520px;
      font-size: 16px;
      line-height: 1.9;
      color: #6b7280;
    }
    &__feature {
      padding: 0;
      margin: 0;
      list-style: none;
      li {
        position: relative;
        margin-bottom: 14px;
        padding-left: 18px;
        color: #4b5563;
        &::before {
          content: '';
          position: absolute;
          left: 0;
          top: 10px;
          width: 6px;
          height: 6px;
          border-radius: 50%;
          background: var(--system-primary-color);
          box-shadow: none;
        }
      }
    }
  }
  .login-panel-wrap {
    width: 520px;
    position: relative;
    padding: 32px;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .fixed-top-right {
    position: absolute;
    top: 24px;
    right: 24px;
    z-index: 2;
  }
  .login-panel {
    width: 100%;
    padding: 36px 32px;
    border-radius: 28px;
    background: #ffffff;
    border: 1px solid #ebeef5;
    box-shadow: 0 12px 36px rgba(31, 35, 41, 0.08);
    &__header {
      margin-bottom: 28px;
      h2 {
        margin: 0 0 10px;
        font-size: 28px;
        color: #18222f;
      }
      p {
        margin: 0;
        color: #6b7280;
      }
    }
  }
  .login-mode {
    display: flex;
    padding: 4px;
    margin-bottom: 24px;
    border-radius: 16px;
    background: #f5f7fa;
    &__item {
      flex: 1;
      text-align: center;
      padding: 12px 0;
      border-radius: 12px;
      color: #607089;
      cursor: pointer;
      transition: all 0.25s ease;
      &.is-active {
        background: linear-gradient(135deg, var(--system-primary-color), #6bb7ff);
        color: #fff;
        box-shadow: 0 10px 20px rgba(64, 158, 255, 0.28);
      }
    }
  }
  .form {
    .el-input {
      margin-bottom: 18px;
    }
    .password-icon {
      cursor: pointer;
      color: var(--system-primary-color);
    }
  }
  .captcha-row {
    display: flex;
    gap: 12px;
    margin-bottom: 24px;
  }
  .captcha-box {
    min-width: 132px;
    border-radius: 16px;
    background: #f8fafc;
    border: 1px solid #e5e7eb;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    user-select: none;
    span {
      font-size: 28px;
      font-weight: 700;
      color: #1f2937;
      letter-spacing: 4px;
      transform: skew(-10deg);
    }
    small {
      margin-top: 4px;
      color: #6b7280;
    }
  }
  .submit-btn {
    width: 100%;
    height: 48px;
    border-radius: 14px;
  }
  .qrcode-box {
    text-align: center;
    &__img {
      position: relative;
      width: 220px;
      height: 220px;
      margin: 0 auto 24px;
      padding: 18px;
      border-radius: 24px;
      background: #fff;
      box-shadow: inset 0 0 0 1px #edf2f7;
    }
    h3 {
      margin: 0 0 10px;
      color: #18222f;
    }
    p {
      margin: 0 0 24px;
      color: #6b7280;
      line-height: 1.8;
    }
  }
  .qrcode-grid {
    width: 100%;
    height: 100%;
    border-radius: 16px;
    background:
      linear-gradient(90deg, #111 10px, transparent 10px) 0 0/22px 22px,
      linear-gradient(#111 10px, transparent 10px) 0 0/22px 22px,
      linear-gradient(90deg, transparent 11px, #111 11px, #111 16px, transparent 16px) 0 0/44px 44px,
      linear-gradient(transparent 11px, #111 11px, #111 16px, transparent 16px) 0 0/44px 44px,
      #fff;
  }
  .qrcode-center-logo {
    position: absolute;
    left: 50%;
    top: 50%;
    width: 56px;
    height: 56px;
    border-radius: 18px;
    transform: translate(-50%, -50%);
    background: linear-gradient(135deg, var(--system-primary-color), #7dc4ff);
    color: #fff;
    font-size: 28px;
    font-weight: 700;
    line-height: 56px;
    box-shadow: 0 10px 30px rgba(64, 158, 255, 0.35);
  }
  @media screen and (max-width: 1100px) {
    .login-brand {
      display: none;
    }
    .login-panel-wrap {
      width: 100%;
      padding: 24px;
    }
  }
  @media screen and (max-width: 750px) {
    .login-panel-wrap {
      padding: 16px;
    }
    .login-panel {
      padding: 28px 20px;
      border-radius: 22px;
    }
    .captcha-row {
      flex-direction: column;
    }
    .captcha-box {
      min-height: 88px;
    }
  }
</style>