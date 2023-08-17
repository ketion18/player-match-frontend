<template>
  <template v-if="user">
    <van-cell title="当前用户" :value="user?.username" />
    <van-cell title="修改个人信息" is-link to="/user/update" />
    <van-cell title="我创建的队伍" is-link to="/user/team/create" />
    <van-cell title="我加入的队伍" is-link to="/user/team/join" />
  </template>
</template>

<script setup lang="ts">
import {useRouter} from "vue-router";
import {onMounted, ref} from "vue";
import myAxios from "../plugins/myAxios";
import {showToast} from "vant/es";
import {getCurrentUser} from "../services/user";

/*const user = {
    id: 1,
    username: 'kobe',
    userAccount: 'kobe',
    avatarUrl: 'https://so1.360tres.com/dr/270_500_/t01f517f2fd44f3ee24.png?size=332x362',
    gender: '男',
    phone: '12345678901',
    email: '1583563833@qq.com',
    planetCode: '24',
    createTime: new Date(),
}*/

const user = ref('');

onMounted(async () => {
  user.value = await getCurrentUser();
})

const router = useRouter();

const toEdit = (editKey: string, editName: string, currentValue: string) => {
  router.push({
    path: '/user/edit',
    query: {
      editKey,
      editName,
      currentValue,
    }
  })
}
</script>

<style scoped>

</style>