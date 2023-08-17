<template>
  <div id="teamPage">
    <van-search v-model="searchText" placeholder="搜索队伍" @search="onSearch" />
    <div style="display: flex; justify-content: center; align-items: center;">
      <van-button type="primary" @click="doJoinTeam" >创建队伍</van-button>
    </div>
    <team-card-list :teamList="teamList" />
    <van-empty v-if="!teamList || teamList.length < 1" description="数据为空" />
  </div>
</template>

<script setup lang="ts">

import {useRouter} from "vue-router";
import TeamCardList from "../components/TeamCardList.vue";
import {onMounted, ref} from "vue";
import myAxios from "../plugins/myAxios";
import {showFailToast} from "vant/es";

const router = useRouter();
const searchText = ref('');

//跳转到加入队伍页
const doJoinTeam = () => {
  router.push({
    path: "/team/add"
  })
}

const teamList = ref([]);

/**
 * 搜索队伍
 */
const listTeam = async (val = '') => {
  const res = await myAxios.get("/team/list/my/create", {
    params: {
      searchText: val,
      pageNum: 1,
    },
  });
  if (res?.code === 0) {
    teamList.value = res.data;
  } else {
    showFailToast('加载队伍失败,请刷新重试');
  }
};

//只会在页面加载时触发一次
onMounted( () => {
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