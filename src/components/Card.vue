<template>
    <a-card  v-if="this.activity.active"  hoverable style="width: 300px" :tab-list="tabList" :title="this.activity.name" @tabChange="key => onTabChange(key)">
      <template #actions>
        <setting-outlined key="setting" />
        <edit-outlined key="edit" />
        <ellipsis-outlined key="ellipsis" />
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
  import { SettingOutlined, EditOutlined, EllipsisOutlined } from '@ant-design/icons-vue';
  import { defineComponent, ref } from 'vue';
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
      SettingOutlined,
      EditOutlined,
      EllipsisOutlined,
      List,
    },
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
    setup(){
      const onTabChange = (value: string) => {
        key.value = value;
      };
      const printDateAndTime = printDateTime;
      const key = ref('description');
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
        onTabChange,
        printDateAndTime,
      }
    }
  });
  </script>
  
  