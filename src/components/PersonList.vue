<template>
  <a-modal 
    :title="title" 
    :visible="this.visiable"
    @ok="handleFinish">
    <template #footer>
      <a-button key="submit" type="primary" @click="handleFinish">确定</a-button>
    </template>

    <a-list item-layout="horizontal" :data-source="data">
      <template #renderItem="{ item }">
        <a-list-item>
          <a-list-item-meta>
            <template #title>
              <p>{{ item.name }}</p>
            </template>
          </a-list-item-meta>
        
          <template #actions>
            <a-popconfirm 
              v-if="!checkedOnly"
              title="同意此人参加活动？"
              ok-text="是"
              cancel-text="否"
              @confirm="() =>{agree(item.name,true);}"
            >
              <a>批准</a>
            </a-popconfirm>
            <a-popconfirm
              v-if="!checkedOnly"
              title="拒绝此人参加活动？"
              ok-text="是"
              cancel-text="否"
              @confirm="() =>{agree(item.name,false);}"
            >
              <a>否决</a>
            </a-popconfirm>
          </template>
        </a-list-item>
      </template>
    </a-list>

  </a-modal>
</template>

<script lang="ts">
  import { List } from 'ant-design-vue';
import { ref } from 'vue';
  export default({
    props: {
      visiable:Boolean,
      data:List,
      checkedOnly:Boolean,
    },
    computed:{
      title() {
        return this.checkedOnly ? "参与人员" : "申请人员";
      }
    },
    emits: ['finished','agree'],
    setup(props,context) {
      const handleFinish = ()=>{
        context.emit("finished");
        console.log("List:"+props.visiable);
      };
      const agree = (name:string,agree:boolean) => {
        console.log("agree:"+agree);
        context.emit("agree",name,agree);
      }
      return {
        handleFinish,
        confirm,
        agree
      };
    }
  });
</script>