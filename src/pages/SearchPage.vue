<template>
  <form action="/">
    <van-search
        v-model="searchText"
        show-action
        placeholder="请输入要搜索的标签"
        @search="onSearch"
        @cancel="onCancel"
    />
  </form>
  <van-divider content-position="left">已选标签</van-divider>
  <div v-if="activeIds.length === 0">请选择标签</div>
  <van-row gutter="16" style="padding: 0 16px">
    <van-col v-for="tag in activeIds">
      <van-tag closeable size="small" type="primary" @close="doClose(tag)">
        {{ tag }}
      </van-tag>
    </van-col>
  </van-row>
  <van-divider content-position="left">可选标签</van-divider>
  <van-tree-select
      v-model:active-id="activeIds"
      v-model:main-active-index="activeIndex"
      :items="tagList"
  />
  <div style="padding: 12px">
    <van-button round block type="primary" @click="doSearchResult">搜索</van-button>
  </div>
</template>

<script setup lang="ts">
import {ref} from 'vue';
import {Toast} from 'vant';
import {useRouter} from "vue-router";

const router = useRouter()

const searchText = ref('');

const originTagList = [
  {
    text: '位置',
    children: [
      {text: '1号位', id: '1号位'},
      {text: '2号位', id: '2号位'},
      {text: '3号位', id: '3号位'},
      {text: '4号位', id: '4号位'},
      {text: '5号位', id: '5号位'},
    ],
  },
  {
    text: '突投能力',
    children: [
      {text: '能突能投', id: '能突能投'},
      {text: '十突零投', id: '十突零投'},
      {text: '零突十投', id: '零突十投'},
      {text: '不突不投', id: '不突不投'},
      {text: '地板流', id: '地板流'},
      {text: '飞天遁地流', id: '飞天遁地流'},
    ],
  },
  {
    text: '擅长技术',
    children: [
      {text: '中距离', id: '中距离'},
      {text: '三分', id: '三分'},
      {text: '篮板', id: '篮板'},
      {text: '组织', id: '组织'},
      {text: '控球', id: '控球'},
      {text: '传球', id: '传球'},
      {text: '转身', id: '转身'},
      {text: '后仰', id: '后仰'},
      {text: '背身技术', id: '背身技术'},
    ],
  },
]
//标签列表
let tagList = ref(originTagList);

/**
 * 搜索过滤
 * @param val
 */
const onSearch = (val) => {
  tagList.value = originTagList.map(parentTag => {
    const tempChildren = [...parentTag.children];
    const tempParentTag = {...parentTag};
    tempParentTag.children = tempChildren.filter(item => item.text.includes(searchText.value));
    return tempParentTag;
  });
}
const onCancel = () => {
  searchText.value = '';
  tagList.value = originTagList;
}

//已选中的标签
const activeIds = ref([]);
const activeIndex = ref(0);

//关联×,移除标签
const doClose = (tag) => {
  activeIds.value = activeIds.value.filter(item => {
    return item !== tag;
  })
}

const doSearchResult = () => {
  router.push({
    path: 'user/list',
    query: {
      tags: activeIds.value
    }
  })
}

</script>

<style scoped>

</style>