<template>
  <user-card-list :user-list="userList" />
  <van-empty v-if="!userList || userList.length < 1" description="搜索结果为空" />
</template>

<script setup lang="ts">
import {onMounted, ref} from "vue";
import {useRoute} from "vue-router";
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import {showToast} from "vant/es";
import qs from 'qs';
import UserCardList from "../components/UserCardList.vue";

const route = useRoute();
const { tags } = route.query;

const userList = ref([]);

onMounted(async () => {
  const userListData = await myAxios.get('/user/search/tags', {
    params: {
      tagNameList: tags
    },
    paramsSerializer: params => {
      return qs.stringify(params, {indices: false})
    }
  })
  .then(function (response) {
    console.log('/user/search/tags', response);
    // showToast('请求成功');
    return response?.data;
  })
  .catch(function (error) {
    console.error('/user/search/tags', error);
    showToast('请求失败');
  })
  if (userListData) {
    userListData.forEach(user => {
      if (user.tags) {
        user.tags = JSON.parse(user.tags);
      }
    })
    userList.value = userListData;
  }
})


/*const mockUser = {
  id: 1,
  username: 'Kobe',
  userAccount: 'kobe',
  avatarUrl: 'https://so1.360tres.com/dr/270_500_/t01f517f2fd44f3ee24.png?size=332x362',
  gender: '1',
  profile: '5 NBA championship, 2 NBA finals MVP,',
  phone: '12345678901',
  email: '1583563833@qq.com',
  userRole: 0,
  planetCode: '24',
  tags: ['得分后卫', '首轮秀', '2号位', '首轮秀', '2号位'],
  createTime: new Date(),
}*/





</script>

<style scoped>

</style>