<template>
  <a-layout style="min-height: 100vh" has-sider>
    <a-layout-sider :collapsed="collapsed" collapsible>
      <div class="logo" />
      <a-menu v-model:selectedKeys="selectedKeys" theme="dark" mode="inline">
        <a-menu-item key="1">
          <pie-chart-outlined />
          <span>{{ menuItems['1'] }}</span>
        </a-menu-item>
        <a-menu-item key="2" v-if="this.currentUser.admin">
          <desktop-outlined />
          <span>{{menuItems['2']}}</span>
        </a-menu-item>
        <a-menu-item key="3">
          <desktop-outlined />
          <span>{{menuItems['3']}}</span>
        </a-menu-item>
        <a-sub-menu key="sub1">
          <template #title>
            <span>
              <user-outlined />
              <span>{{menuItems['sub1']}}</span>
            </span>
          </template>
          <a-menu-item key="4">{{menuItems['4']}}</a-menu-item>
          <a-menu-item key="5">{{menuItems['5']}}</a-menu-item>
          <a-menu-item key="6">{{menuItems['6']}}</a-menu-item>
        </a-sub-menu>
        <a-menu-item key="7">
          <desktop-outlined />
          <span>{{menuItems['7']}}</span>
        </a-menu-item>
      </a-menu>
    </a-layout-sider>
    <a-layout>
      <a-layout-header style="background: #fff; padding: 0" />
      <a-layout-content style="margin: 0 16px">
        <a-breadcrumb style="margin: 16px 0">
          <a-breadcrumb-item v-for="key in selectedKeys">{{menuItems[key]}}</a-breadcrumb-item>
        </a-breadcrumb>
        <div :style="{ padding: '24px', background: '#fff', minHeight: '360px' }">
          <div v-if="selectedKeys[0] === '1'">
            <a-descriptions
              :title=this.currentUser.name
              bordered
              :column="1"
            >
              <a-descriptions-item label="管理员">{{yesNo}}</a-descriptions-item>
            </a-descriptions>
          </div>
          <div v-else-if="selectedKeys[0] === '2'" style="display: flex; align-items:center">
            <Card v-for="activity in activityList" :activity="activity" style="margin:0 10px" />
            <a-button type="primary" shape="circle" size="large" @click="()=>NewActivityData.visiable=true">
              <template #icon>
                <plus-outlined />
              </template>
            </a-button>
          </div>
          <div v-else-if="selectedKeys[0] === '3'" style="display: flex;">
            <Card v-for="activity in activityList" :activity="activity" style="margin:0 10px"/>
          </div>
          <NewActivity :visiable="NewActivityData.visiable" @finished="()=>NewActivityData.visiable=false"/>
        </div>
      </a-layout-content>
      <a-layout-footer style="text-align: center">
        CHOSER Tech ©2022 by Justin Li <br/>
        Ant Design ©2018 Created by Ant UED
      </a-layout-footer>
    </a-layout>
  </a-layout>
</template>
<script lang="ts">
import {
  PieChartOutlined,
  DesktopOutlined,
  UserOutlined,
  TeamOutlined,
  FileOutlined,
  PlusOutlined,
} from '@ant-design/icons-vue';
import { defineComponent, ref } from 'vue';
import axios from 'axios';
import Card from './Card.vue';
import NewActivity from './NewActivity.vue';

export default defineComponent({
  components: {
    PieChartOutlined,
    DesktopOutlined,
    UserOutlined,
    TeamOutlined,
    FileOutlined,
    PlusOutlined,
    Card,
    NewActivity,
  },
  props: {
    currentUser : Object,
    backendUrl : String,
  },

  watch: {
    selectedKeys(newSelectedKeys){
      if (newSelectedKeys[0]==='2') {
        console.log(this.currentUser);
        axios({
          method:'post',
          url:this.backendUrl,
          data: {
            header:'CreatedActivity',
            user: {
              name:this.currentUser.name,
              admin:this.currentUser.admin,
            }
          }
        })
        .then((response: any)=>{
          this.activityList = response.data.activities;
        })
        .catch(err=>console.log(err));
      } else if (newSelectedKeys[0]==='3') {
        console.log(this.currentUser);
        axios({
          method:'post',
          url:this.backendUrl,
          data: {
            header:'AllActivity',
          }
        })
        .then((response: any)=>{
          this.activityList = response.data.activities;
        })
        .catch(err=>console.log(err));
      }
    }
  },

  computed: {
    yesNo(){
      if(this.currentUser.admin) {
        return '是';
      } else {
        return '否';
      }
    }
  },

  setup(){
    const qwq: {[key:string]:string} = {
      '1': '我的资料',
      '2': '发布的活动',
      '3': '发现活动',
      'sub1': '申请的活动',
      '4':'已通过的活动',
      '5':'被拒绝的活动',
      '6':'全部活动',
      '7':'发布活动',
    };
    const menuItems = ref(qwq);
    const NewActivityData = ref({
      visiable:false,
    })

    return {
      menuItems,
      NewActivityData
    };
  },
  
  data() {
    return {
      activityList: ref<object[]>(),
      collapsed: ref<boolean>(false),
      selectedKeys: ref<string[]>(['1']),
    };
  },
});
</script>
<style>
#components-layout-demo-side .logo {
  height: 32px;
  margin: 16px;
  background: rgba(255, 255, 255, 0.3);
}

.site-layout .site-layout-background {
  background: #fff;
}
[data-theme='dark'] .site-layout .site-layout-background {
  background: #141414;
}
</style>
