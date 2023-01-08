<template>
    <a-list item-layout="horizontal" :data-source="datas" size="small">
      <template #renderItem="{ item }">
        <a-list-item>
          <a-list-item-meta
            :description="item.description"
          >
            <template #title>
              <p>{{ item.title }}</p>
            </template>
          </a-list-item-meta>
        </a-list-item>
      </template>
    </a-list>
  </template>
  <script lang="ts">
  import { defineComponent } from 'vue';
  export default defineComponent({
    props: {
        datas:Array
    }
  });
  </script>