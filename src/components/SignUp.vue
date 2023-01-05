<template>
    <a-form
      ref="formRef"
      name="custom-validation"
      :model="formState"
      :rules="rules"
      v-bind="layout"
      @finish="handleFinish"
      @validate="handleValidate"
      @finishFailed="handleFinishFailed"
    >
      <a-form-item label="用户名" name="username" :rules="[{ required: true, message: '请输入用户名！' }]">
        <a-input v-model:value="formState.username">
          <template #prefix>
            <UserOutlined class="site-form-item-icon" />
          </template>
        </a-input>
      </a-form-item>
      <a-form-item has-feedback label="密码" name="pass">
        <a-input v-model:value="formState.pass" type="password" autocomplete="off">
          <template #prefix>
            <LockOutlined class="site-form-item-icon" />
          </template>
        </a-input>
      </a-form-item>
      <a-form-item has-feedback label="确认密码" name="checkPass">
        <a-input v-model:value="formState.checkPass" type="password" autocomplete="off">
          <template #prefix>
            <LockOutlined class="site-form-item-icon" />
          </template>
        </a-input>
      </a-form-item>
      <a-form-item :wrapper-col="{ span: 14, offset: 4 }">
        <a-button type="primary" html-type="submit">注册</a-button>
        <a-button style="margin-left: 10px" @click="goback">返回</a-button>
      </a-form-item>
    </a-form>
  </template>

  <script lang="ts">
  import type { Rule } from 'ant-design-vue/es/form';
  import { defineComponent, reactive, ref } from 'vue';
  import { UserOutlined, LockOutlined } from '@ant-design/icons-vue';
  import type { FormInstance } from 'ant-design-vue';

  interface FormState {
    username: string;
    pass: string;
    checkPass: string;
    admin: boolean;
  }

  export default defineComponent({

    components: {
      UserOutlined,
      LockOutlined,
    },

    emits: ['signup','go-back'],

    setup(props,context) {

      const formRef = ref<FormInstance>();
      const formState = reactive<FormState>({
        username: '',
        pass: '',
        checkPass: '',
        admin: false,
      });

      let validatePass = async (_rule: Rule, value: string) => {
        if (value === '') {
          return Promise.reject('请输入密码');
        } else {
          if (formState.checkPass !== '') {
            formRef.value.validateFields('checkPass');
          }
          return Promise.resolve();
        }
      };

      let validatePass2 = async (_rule: Rule, value: string) => {
        if (value === '') {
          return Promise.reject('请再次输入密码');
        } else if (value !== formState.pass) {
          return Promise.reject("两次输入的密码不一致");
        } else {
          return Promise.resolve();
        }
      };
  
      const rules: Record<string, Rule[]> = {
        pass: [{ required: true, validator: validatePass, trigger: 'change' }],
        checkPass: [{ validator: validatePass2, trigger: 'change' }],
      };

      const layout = {
        labelCol: { span: 6 },
        wrapperCol: { span: 16 },
      };

      const handleFinish = (values: FormState) => {
        console.log("Sign Up",values, formState);
        context.emit('signup',formState);
      };

      const handleFinishFailed = (errors : any) => {
        console.log(errors);
      };

      const goback = () => {
        context.emit('signup');
      };

      const handleValidate = (...args : any[]) => {
        console.log(args);
      };
      
      return {
        formState,
        formRef,
        rules,
        layout,
        UserOutlined,
        LockOutlined,
        handleFinishFailed,
        handleFinish,
        goback,
        handleValidate,
      };
    },
  });
  </script>