<template>
    <a-card  v-if="this.activity.active"  hoverable style="width: 300px" :tab-list="tabList" :title="this.activity.name" @tabChange="key => onTabChange(key)">
      <template #actions>
        <div>  
          <question-circle-outlined v-if="!this.activity.checked"/>
          <check-circle-outlined v-else-if="this.activity.accepted"/>
          <close-circle-outlined v-else/>
        </div>
        <a-popconfirm
          title="确定删除此申请？"
          ok-text="是"
          cancel-text="否"
          @confirm="confirm"
        >
          <delete-outlined />
        </a-popconfirm>
      </template>

      <!-- <div >
        <p>{{ this.activity.description }}</p>
        <p style="color:var(--vt-c-text-light-2)">创建者： {{ this.activity.creator }}</p>
        <p style="color:var(--vt-c-text-light-2)">修改时间： {{ printDateAndTime(this.activity.modified_time) }}</p>
      </div> -->
      <List v-if="key==='description'" :datas="description"/>
      
      <List v-else-if="key==='time'" :datas="time"/>
      <div v-else-if="key==='address'">
        <p>{{ this.activity.address }}</p>
      </div>
    </a-card>
  </template>
  <script lang="ts">
  import { DeleteOutlined , CheckCircleOutlined , QuestionCircleOutlined , CloseCircleOutlined } from '@ant-design/icons-vue';
  import { defineComponent, ref } from 'vue';
  import { message } from 'ant-design-vue';
  import List from './List.vue';
  function printDateTime(timestamp:number) : String{
        let date = new Date(timestamp);
        return date.getFullYear() + "年" + (date.getMonth()+1) + "月" + date.getDate() + "日 " + date.getHours() + ":" + date.getMinutes() + ":" + date.getSeconds();
  }
  export default defineComponent({
    props:{
        activity:Object
    },
    components: {
      DeleteOutlined,
      CheckCircleOutlined,
      QuestionCircleOutlined,
      CloseCircleOutlined,
      List,
    },
    emits: ['refresh'],
    computed:{
      description(){
        return [
          {
            title:this.activity.description,
            description: ""
          },
          {
            title:"创建者",
            description: this.activity.creator
          },
          {
            title:"人数需求",
            description: this.activity.person_needed
          },
          {
            title:"",
            description: "修改时间：" + printDateTime(this.activity.modified_time)
          },
        ];
      },
      time() {
        return [
          {
            title:"报名截止时间",
            description: printDateTime(this.activity.deadline)
          },
          {
            title:"开始时间",
            description: printDateTime(this.activity.start_time)
          },
          {
            title:"结束时间",
            description: printDateTime(this.activity.end_time)
          },
        ];
      },
    },
    setup(props,context){
      const onTabChange = (value: string) => {
        key.value = value;
      };
      const printDateAndTime = printDateTime;
      const key = ref('description');
      const confirm = (e: MouseEvent)=>{
        message.success('Click on Yes');
        context.emit('refresh');
      };
      const tabList = [
        {
          key: 'description',
          tab: '介绍',
        },
        {
          key: 'time',
          tab: '时间',
        },
        {
          key: 'address',
          tab: '地址',
        },
      ];
      return {
        key,
        tabList,
        confirm,
        onTabChange,
        printDateAndTime,
      }
    }
  });
  </script>
  
  