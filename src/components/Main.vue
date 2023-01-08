<template>
  <a-layout style="min-height: 100vh" has-sider>
    <a-layout-sider :collapsed="collapsed" collapsible>
      <div class="logo" />
      <a-menu v-model:selectedKeys="selectedKeys" theme="dark" mode="inline">
        <a-menu-item key="1">
          <user-outlined />
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
              <pie-chart-outlined />
              <span>{{menuItems['sub1']}}</span>
            </span>
          </template>
          <a-menu-item key="4">{{menuItems['4']}}</a-menu-item>
          <a-menu-item key="5">{{menuItems['5']}}</a-menu-item>
          <a-menu-item key="6">{{menuItems['6']}}</a-menu-item>
        </a-sub-menu>
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
            <MyCard v-for="activity in activityList.list" :activity="activity" style="margin:0 10px" @showPerson="showPerson" @delete-activity="deleteActivity" @refresh="getNewMyAc"/>
            <a-button type="primary" shape="circle" size="large" @click="()=>NewActivityData.visiable=true">
              <template #icon>
                <plus-outlined />
              </template>
            </a-button>
          </div>
          <div v-else-if="selectedKeys[0] === '3'" style="display: flex;">
            <Card v-for="activity in activityList.list" :activity="activity" style="margin:0 10px" @apply="apply"/>
          </div>
          <div v-else-if="selectedKeys[0] === '4'" style="display: flex;">
            <ApplicationCard v-for="application in acceptedApplication" :activity="application" style="margin:0 10px" @apply="apply"/></div>
          <div v-else-if="selectedKeys[0] === '5'" style="display: flex;">
            <ApplicationCard v-for="application in unacceptedApplication" :activity="application" style="margin:0 10px" @apply="apply"/></div>
          <div v-else-if="selectedKeys[0] === '6'" style="display: flex;">
            <ApplicationCard v-for="application in applicationList.list" :activity="application" style="margin:0 10px" @apply="apply"/>
          </div>

          <NewActivity :visiable="NewActivityData.visiable" @finished="newActivity" @canceled="()=>NewActivityData.visiable=false"/>
          <PersonList :checkedOnly="PersonListData.checkedOnly" :visiable="PersonListData.visiable" :data="PersonListData.persons" @finished="()=>PersonListData.visiable=false" @agree="agree"/>
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
import { defineComponent, ref , reactive } from 'vue';
import axios from 'axios';
import Card from './Card.vue';
import MyCard from './MyCard.vue';
import NewActivity from './NewActivity.vue';
import ApplicationCard from './ApplicationCard.vue';
import PersonList from './PersonList.vue';

// function refreshList(currentUser:any,backendUrl:string,qwq:object,selectedKeys:String[]) {
//   switch (selectedKeys[0]) {
//     case '2':
//     console.log(currentUser);
//     axios({
//       method:'post',
//       url:backendUrl,
//       data: {
//         header:'CreatedActivity',
//         user: {
//           name:currentUser.name,
//           admin:currentUser.admin,
//         }
//       }
//     })
//     .then((response: any)=>{
//       qwq.activity = response.data.activities;
//     })
//     .catch(err=>console.log(err));
//     break;
//   case '3':
//     console.log(currentUser);
//     axios({
//       method:'post',
//       url:backendUrl,
//       data: {
//         header:'AllActivity',
//       }
//     })
//     .then((response: any)=>{
//       qwq.activity = response.data.activities;
//     })
//     .catch(err=>console.log(err));
//     break;
//   case '4':
//   case '5':
//   case '6':
//     console.log(currentUser);
//     axios({
//       method:'post',
//       url:backendUrl,
//       data: {
//         header:'AppliedActivity',
//         user:currentUser.name,
//       }
//     })
//     .then((response: any)=>{
//       qwq.application = response.data.activities;
//     })
//     .catch(err=>console.log(err));
//     break;
//   }
// }


export default defineComponent({
  components: {
    PieChartOutlined,
    DesktopOutlined,
    UserOutlined,
    TeamOutlined,
    FileOutlined,
    PlusOutlined,
    Card,
    MyCard,
    NewActivity,
    ApplicationCard,
    PersonList,
  },
  props: {
    currentUser : Object,
    backendUrl : String,
  },

  watch: {
    selectedKeys(newSelectedKeys){
      switch (newSelectedKeys[0]) {
        case '2':
          this.getNewMyAc();
          break;
        case '3':
          this.getNewAllAc();
          break;
        case '4':
        case '5':
        case '6':
          this.getAllAppl();
          break;
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
    },
    checkedApplication() {
      return this.applicationList.list.filter((t) => t.checked);
    },
    acceptedApplication() {
      return this.applicationList.list.filter((t) => t.checked).filter((t) => t.accepted);
    },
    unacceptedApplication() {
      return this.applicationList.list.filter((t) => t.checked).filter((t) => !t.accepted);
    },
  },

  setup(props,context){
    const qwq: {[key:string]:string} = {
      '1': '我的资料',
      '2': '发布的活动',
      '3': '发现活动',
      'sub1': '申请的活动',
      '4':'已通过的活动',
      '5':'被拒绝的活动',
      '6':'全部活动',
    };
    const menuItems = ref(qwq);
    const NewActivityData = ref({
      visiable:false,
    });

    const PersonListData = ref({
      visiable:false,
      persons:[],
      id:0,
      checkedOnly:false,
    });

    const activityList = reactive({list:[]});
    const applicationList = reactive({list:[]});

    const apply = (id:number) => {
      axios({
          method:'post',
          url:props.backendUrl,
          data: {
            header:'Apply',
            apply: {
              id:id,
              user:props.currentUser.name,
            }
          }
        })
        .then((response: any)=>{
        })
        .catch(err=>console.log(err));
    };

    const getNewMyAc = () => {
      axios({
        method:'post',
        url:props.backendUrl,
        data: {
          header:'CreatedActivity',
          user: {
            name:props.currentUser.name,
            admin:props.currentUser.admin,
          }
        }
      })
      .then((response: any)=>{
        activityList.list = response.data.activities;
        console.log(activityList.list);
      })
      .catch(err=>console.log(err));
    }

    const getNewAllAc = () => {
      axios({
        method:'post',
        url:props.backendUrl,
        data: {
          header:'AllActivity',
        }
      })
      .then((response: any)=>{
        activityList.list = response.data.activities;
        console.log(activityList.list);
      })
      .catch(err=>console.log(err));
    }

    const getAllAppl = () => {
      axios({
        method:'post',
        url:props.backendUrl,
        data: {
          header:'AppliedActivity',
          user:props.currentUser.name,
        }
      })
      .then((response: any)=>{
        applicationList.list = response.data.activities;
        console.log(applicationList.list);
      })
      .catch(err=>console.log(err));
    }

    const newActivity = (formState:any) => {
      axios({
          method:'post',
          url:props.backendUrl,
          data: {
            header:'NewActivity',
            info:{
              creator:props.currentUser.name,
              name:formState.name,
              start_time:formState.time[0],
              end_time:formState.time[1],
              deadline:formState.deadline,
              person_needed:formState.person_needed,
              description:formState.description,
              address:formState.address,
            }
          }
        })
        .then((response: any)=>{
        })
        .catch(err=>console.log(err));

        NewActivityData.value.visiable=false;

        getNewMyAc();
    }

    const showPerson = (id:number, checked:boolean) => {
      console.log('Main: show person');
      axios({
          method:'post',
          url:props.backendUrl,
          data: {
            header:'GetApplicants',
            id:id
          }
        })
        .then((response: any)=>{
          PersonListData.value.persons = checked ? response.data.applicants.filter((t)=>t.checked) : response.data.applicants.filter((t)=>!t.checked);
        })
        .catch(err=>console.log(err));
        
      PersonListData.value.visiable = true;
      PersonListData.value.id=id;
      PersonListData.value.checkedOnly=checked;
    };

    const deleteActivity = (id:number) => {
      axios({
          method:'post',
          url:props.backendUrl,
          data: {
            header:'DropActivity',
            id:id
          }
        })
        .then((response: any)=>{
        })
        .catch(err=>console.log(err));
    }

    const agree = (name:string,agree:boolean) => {
      axios({
          method:'post',
          url:props.backendUrl,
          data: {
            header:'Agree',
            data:{
              name,
              id:PersonListData.value.id,
              agree,
            }
          }
        })
        .then((response: any)=>{
          axios({
          method:'post',
          url:props.backendUrl,
          data: {
            header:'GetApplicants',
            id:PersonListData.value.id
          }
        })
        .then((response: any)=>{
          PersonListData.value.persons = PersonListData.value.checkedOnly ? response.data.applicants.filter((t)=>t.checked) : response.data.applicants.filter((t)=>!t.checked);
        })
        .catch(err=>console.log(err));
        })
        .catch(err=>console.log(err));
        
      PersonListData.value.visiable = true;
    }

    return {
      menuItems,
      NewActivityData,
      PersonListData,
      activityList,
      applicationList,
      apply,
      showPerson,
      newActivity,
      deleteActivity,
      getNewMyAc,
      getNewAllAc,
      getAllAppl,
      agree
    };
  },
  
  data() {
    return {
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
