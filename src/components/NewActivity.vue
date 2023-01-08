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
      :model="formState"
    >
      <a-form-item label="名称" name="name" :rules="[{ required: true}]">
        <a-input v-model:value="formState['name']"/>
      </a-form-item>
      <a-form-item name="description" label="介绍" :rules="[{ required: true}]">
        <a-textarea v-model:value="formState['description']"/>
      </a-form-item>
      <a-form-item name="range-time-picker" label="持续时间" v-bind="config">
        <a-range-picker
          show-time
          format="YYYY-MM-DD HH:mm:ss"
          value-format="YYYY-MM-DD HH:mm:ss"
          v-model:value="formState['time']"
        />
      </a-form-item>
      <a-form-item name="deadline-picker" label="报名截止时间" v-bind="rangeConfig">
        <a-date-picker
          show-time
          format="YYYY-MM-DD HH:mm:ss"
          value-format="YYYY-MM-DD HH:mm:ss"
          v-model:value="formState['deadline']"
        />
      </a-form-item>
      <a-form-item name="address" label="地址" :rules="[{ required: true}]">
        <a-textarea v-model:value="formState['address']"/>
      </a-form-item>
      <a-form-item :wrapper-col="{ span: 14, offset: 4 }">
      </a-form-item>
      <a-form-item label="需求人数" name="needed-person">
        <a-input-number v-model:value="formState['person_needed']" />
      </a-form-item>
    </a-form>
  </a-modal>
</template>

<script lang="ts">
import { ref, reactive } from 'vue';
import { message } from 'ant-design-vue';
import type { FormInstance } from 'ant-design-vue';

interface FormState {
  'name':string;
  'time':[string,string];
  'deadline':string;
  'person_needed':number;
  'description':string;
  'address':string;
}

export default({
  props: {
    visiable:Boolean
  },
  emits: ['finished','canceled'],
  setup(props,context) {
    const config = {
      rules: [{ type: 'string' as const}],
    };
    const rangeConfig = {
      rules: [{ type: 'array' as const}],
    };
    const handleFinish = ()=>{
      context.emit("finished",formState);
      message.success("活动已创建");
    }
    const handleCancel = ()=> {
      context.emit("canceled");
    }
    const formRef = ref<FormInstance>();
    const formState = reactive({} as FormState);
    return {
      config,
      rangeConfig,
      handleFinish,
      handleCancel,
      formRef,
      formState,
    };
  }
});
</script>