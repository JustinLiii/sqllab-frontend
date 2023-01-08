<!-- <template>
  <a-modal v-model:visible="visible" title="Basic Drawer" @ok="onOk">
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
      <a-form-item label="名称" name="name" :rules="[{ required: true}]">
        <a-input v-model:value="formState.username">
          <template #prefix>
            <UserOutlined class="site-form-item-icon" />
          </template>
        </a-input>
      </a-form-item>
      <a-form-item name="description" label="介绍" :rules="[{ required: true}]">
        <a-textarea v-model:value="formState.user.introduction" />
      </a-form-item>
      <a-form-item name="range-time-picker" label="持续时间" v-bind="config">
        <a-range-picker
          v-model:value="formState['range-time-picker']"
          show-time
          format="YYYY-MM-DD HH:mm:ss"
          value-format="YYYY-MM-DD HH:mm:ss"
        />
      </a-form-item>
      <a-form-item name="deadline-picker" label="报名截止时间" v-bind="rangeConfig">
        <a-date-picker
          v-model:value="formState['date-time-picker']"
          show-time
          format="YYYY-MM-DD HH:mm:ss"
          value-format="YYYY-MM-DD HH:mm:ss"
        />
      </a-form-item>
      <a-form-item name="address" label="地址" :rules="[{ required: true}]">
        <a-textarea v-model:value="formState.user.introduction" />
      </a-form-item>
      <a-form-item :wrapper-col="{ span: 14, offset: 4 }">
        <a-button type="primary" html-type="submit">保存</a-button>
        <a-button style="margin-left: 10px" @click="goback">返回</a-button>
      </a-form-item>
    </a-form>
  </a-modal>
</template> -->

<template>
  <a-modal 
    title="创建活动" 
    :visible="this.visiable"
    @ok="handleFinish">
    <template #footer>
        <a-button key="back" @click="handleCancel">取消</a-button>
        <a-button key="submit" type="primary" @click="handleFinish">确定</a-button>
    </template>

    <a-form
      ref="formRef"
      name="custom-validation"
    >
      <a-form-item label="名称" name="name" :rules="[{ required: true}]">
        <a-input/>
      </a-form-item>
      <a-form-item name="description" label="介绍" :rules="[{ required: true}]">
        <a-textarea/>
      </a-form-item>
      <a-form-item name="range-time-picker" label="持续时间" v-bind="config">
        <a-range-picker
          show-time
          format="YYYY-MM-DD HH:mm:ss"
          value-format="YYYY-MM-DD HH:mm:ss"
        />
      </a-form-item>
      <a-form-item name="deadline-picker" label="报名截止时间" v-bind="rangeConfig">
        <a-date-picker
          show-time
          format="YYYY-MM-DD HH:mm:ss"
          value-format="YYYY-MM-DD HH:mm:ss"
        />
      </a-form-item>
      <a-form-item name="address" label="地址" :rules="[{ required: true}]">
        <a-textarea/>
      </a-form-item>
      <a-form-item :wrapper-col="{ span: 14, offset: 4 }">
        <!-- <a-button type="primary" html-type="submit">保存</a-button>
        <a-button style="margin-left: 10px" >返回</a-button> -->
      </a-form-item>
    </a-form>
  </a-modal>
</template>

<script lang="ts">
import { message } from 'ant-design-vue';
export default({
  props: {
    visiable:Boolean
  },
  emits: ['finished'],
  setup(props,context) {
    const config = {
      rules: [{ type: 'string' as const, required: true, message: 'Please select time!' }],
    };
    const rangeConfig = {
      rules: [{ type: 'array' as const, required: true, message: 'Please select time!' }],
    };
    const handleFinish = ()=>{
      message.success("活动已创建");
      context.emit("finished");
    }
    const handleCancel = ()=> {
      handleFinish();
    }
    return {
      config,
      rangeConfig,
      handleFinish,
      handleCancel,
    };
  }
});
</script>