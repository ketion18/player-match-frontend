<template>
  <div id="teamCardList">
    <van-card
        v-for="team in props.teamList"
        :thumb="team1"
        :desc=team.description
        :title="`${team.name}`"
    >
      <template #tags>
        <van-tag plain type="danger" style="margin-right: 8px;margin-top: 8px">
          {{
            teamStatusEnum[team.status]
          }}
        </van-tag>
      </template>
      <template #bottom>
        <div>
          {{ `球队人数: ${team.hasJoinNum}/${team.maxNum}`}}
        </div>
        <div v-if="team.expireTime">
          {{ '过期时间: ' + team.expireTime }}
        </div>
        <div>
          {{ '创建时间: ' + team.createTime }}
        </div>
      </template>
      <template #footer>
        <!--       todo 仅加入的队伍可见-->
        <van-button v-if="team.userId !== currentUser?.id && team.hasJoin" size="mini" type="danger" plain
                    @click="doQuitTeam(team.id)">退出队伍
        </van-button>
        <van-button v-if="team.userId === currentUser?.id" size="mini" type="danger"
                    @click="doDeleteTeam(team.id)">解散队伍
        </van-button>
        <van-button v-if="team.userId === currentUser?.id" size="mini" type="primary" plain
                    @click="doUpdateTeam(team.id)">更新队伍
        </van-button>
        <van-button v-if="team.userId !== currentUser?.id && !team.hasJoin" size="mini" type="primary"
                    @click="preJoinTeam(team)">加入队伍
        </van-button>
      </template>
    </van-card>
    <van-dialog v-model:show="showPasswordDialog" title="请输入密码" show-cancel-button @confirm="doJoinTeam" @cancel="doJoinCancel">
      <van-field v-model="password" placeholder="请输入密码"/>
    </van-dialog>

  </div>
</template>

<script setup lang="ts">
import {TeamType} from "../models/team";
import {defineProps, onMounted, ref, withDefaults} from "vue";
import {teamStatusEnum} from "../constants/team";
import team1 from '../assets/team1.png';
import myAxios from "../plugins/myAxios";
import {showFailToast, showSuccessToast} from "vant/es";
import {getCurrentUserState} from "../states/user";
import {getCurrentUser} from "../services/user";
import {useRouter} from "vue-router";

interface TeamCardListProps {
  teamList: TeamType[];
}

const props = withDefaults(defineProps<TeamCardListProps>(), {
  teamList: [] as TeamType[],
});

const showPasswordDialog = ref(false);
const joinTeamId = ref(0);
const password = ref('');
const currentUser = ref();

const router = useRouter();

onMounted(async () => {
  currentUser.value = await getCurrentUser();
})

const preJoinTeam = (team: TeamType) => {
  joinTeamId.value = team.id;
    if (team.status === 0) {
      doJoinTeam();
    } else {
      showPasswordDialog.value = true;
    }
}

const doJoinCancel = () => {
  joinTeamId.value = 0;
  password.value = '';
}

/**
 * 加入队伍
 */
const doJoinTeam = async () => {
  if (!joinTeamId.value) {
    return ;
  }
  const res = await myAxios.post("/team/join", {
    teamId: joinTeamId.value,
    password: password.value,
  });
  if (res?.code === 0) {
    showSuccessToast('加入成功');
    window.location.href = "/team";
    doJoinCancel();
  } else {
    showFailToast('加入失败' + (res.description ? `,${res.description}` : ''));
    window.location.href = "/team";
  }
}

/**
 * 跳转至更新队伍页
 * @param id
 */
const doUpdateTeam = (id: number) => {
  router.push({
    path: '/team/update',
    query: {
      id,
    }
  })
}

/**
 * 退出队伍
 * @param id
 */
const doQuitTeam = async (id: number) => {
  const res = await myAxios.post("/team/quit", {
    teamId: id
  });
  if (res?.code === 0) {
    showSuccessToast('操作成功');
    window.location.href = "/team";
  } else {
    showFailToast('操作失败' + (res.description ? `,${res.description}` : ''));
    window.location.href = "/team";
  }
}

/**
 * 解散队伍(删除队伍)
 * @param id
 */
const doDeleteTeam = async (id: number) => {
  const res = await myAxios.post("/team/delete", {
    id,
  });
  if (res?.code === 0) {
    showSuccessToast('操作成功');
    window.location.href = "/team";
  } else {
    showFailToast('操作失败' + (res.description ? `,${res.description}` : ''));
  }
}

</script>

<style scoped>
#teamCardList :deep(.van-image__img) {
  height: 110px;
  object-fit: unset;
}
</style>