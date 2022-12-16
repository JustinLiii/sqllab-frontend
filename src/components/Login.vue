<template>
    <a-form
      :model="formState"
      name="normal_login"
      class="login-form"
      @finish="onFinish"
      @finishFailed="onFinishFailed"
    >
      <a-form-item
        label="用户名"
        name="username"
        :rules="[{ required: true, message: '请输入用户名！' }]"
      >
        <a-input v-model:value="formState.username">
          <template #prefix>
            <UserOutlined class="site-form-item-icon" />
          </template>
        </a-input>
      </a-form-item>
  
      <a-form-item
        label="密码"
        name="password"
        :rules="[{ required: true, message: '请输入密码！' }]"
      >
        <a-input-password v-model:value="formState.password">
          <template #prefix>
            <LockOutlined class="site-form-item-icon" />
          </template>
        </a-input-password>
      </a-form-item>
  
      <a-form-item>
        <a-form-item name="remember" no-style>
          <a-checkbox v-model:checked="formState.remember">记住密码</a-checkbox>
        </a-form-item>
        <a class="login-form-forgot" href="">找回密码</a>
      </a-form-item>
  
      <a-form-item>
        <a-button :disabled="disabled" type="primary" html-type="submit" class="login-form-button">
          登入
        </a-button>
        或
        <a-button type="link" @click="onCreateNewUser">立即注册</a-button>
      </a-form-item>
    </a-form>
  </template>

  <script lang="ts">
  import { defineComponent, reactive, computed } from 'vue';
  import { UserOutlined, LockOutlined } from '@ant-design/icons-vue';

  export default defineComponent({

    components: {
      UserOutlined,
      LockOutlined,
    },

    emits: ['createNewUser','login'],

    setup(props,context) {

      const formState = reactive({
        username: '',
        password: '',
        remember: true,
      });

      const onFinish = (values : any) => {
        context.emit('login',formState);
        console.log('Success:', values);
      };

      const onFinishFailed = (errorInfo : any) => {
        // context.emit('finishFailed');
        console.log('Failed:', errorInfo);
      };

      const onCreateNewUser = () => {
        context.emit('createNewUser');
      };

      const disabled = computed(() => {
        return !(formState.username && formState.password);
      });

      return {
        formState,
        onFinish,
        onFinishFailed,
        onCreateNewUser,
        disabled,
      };
    },
  });
  </script>
  <style>
  #components-form-demo-normal-login .login-form {
    max-width: 300px;
  }
  #components-form-demo-normal-login .login-form-forgot {
    float: right;
  }
  #components-form-demo-normal-login .login-form-button {
    width: 100%;
  }
  </style>