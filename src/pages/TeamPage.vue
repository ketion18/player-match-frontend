<template>
  <div id="teamPage">
    <van-search v-model="searchText" placeholder="搜索队伍" @search="onSearch"/>
    <van-tabs v-model:active="active" @change="onTabChange">
      <van-tab title="公开" name="public"/>
      <van-tab title="加密" name="private"/>
    </van-tabs>
    <div style="display: flex; justify-content: center; align-items: center;">
      <van-button class="add-button" type="primary" @click="toAddTeam">创建队伍</van-button>
    </div>
    <team-card-list :teamList="teamList"/>
    <van-empty v-if="!teamList || teamList.length < 1" description="数据为空"/>
  </div>
</template>

<script setup lang="ts">

import {useRouter} from "vue-router";
import TeamCardList from "../components/TeamCardList.vue";
import {onMounted, ref} from "vue";
import myAxios from "../plugins/myAxios";
import {showFailToast} from "vant/es";

const active = ref('public');
const router = useRouter();
const searchText = ref('');

const onTabChange = (name) => {
  if (name === 'public') {
    //查公开队伍
    listTeam(searchText.value, 0);
  } else {
    //查加密队伍
    listTeam(searchText.value, 2);
  }
}

//跳转到创建队伍页
const toAddTeam = () => {
  router.push({
    path: "/team/add"
  })
}

const teamList = ref([]);

/**
 * 搜索队伍
 */
const listTeam = async (val = '', status = 0) => {
  const res = await myAxios.get("/team/list", {
    params: {
      searchText: val,
      pageNum: 1,
      status,
    },
  });
  if (res?.code === 0) {
    teamList.value = res.data;
  } else {
    showFailToast('加载队伍失败,请刷新重试');
  }
};

//只会在页面加载时触发一次
onMounted(() => {
  listTeam();
})

const onSearch = (val) => {
  listTeam(val);
};

</script>

<style scoped>
#teamPage {

}
</style>