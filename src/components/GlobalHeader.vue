<template>
  <a-row id="globalHeader" align="center" :wrap="false">
    <a-col flex="auto">
      <a-menu
          mode="horizontal"
          :selected-keys="selectedKeys"
          @menu-item-click="doMenuClick"
      >
        <a-menu-item
            key="0"
            :style="{ padding: 0, marginRight: '38px' }"
            disabled
        >
          <div class="title-bar">
            <img class="logo" src="../assets/logo.svg"/>
            <div class="title">星海 OJ</div>
          </div>
        </a-menu-item>
        <a-menu-item v-for="item in visibleRoutes" :key="item.path">
          {{ item.name }}
        </a-menu-item>
      </a-menu>
    </a-col>
    <a-col flex="100px">
      <div class="user-info">
        <span>欢迎您！{{ store.state.user?.loginUser?.userName }}</span>
        <a-button type="primary" @click="handleLogin">
          登录/切换
        </a-button>
        <!-- 如果未登录，显示“登录”按钮 -->
        <!-- 如果已登录，显示“退出”按钮 -->
        <!--<a-button v-else type="primary" @click="handleLogout">
          退出
        </a-button>-->
      </div>
    </a-col>
  </a-row>
</template>

<script setup lang="ts">
import {routes} from "../router/routes";
import {useRouter} from "vue-router";
import {computed, ref} from "vue";
import {useStore} from "vuex";
import checkAccess from "@/access/checkAccess";
import ACCESS_ENUM from "@/access/accessEnum";

const router = useRouter();
const store = useStore();

// 判断用户是否登录
// const isLoggedIn = computed(() => {
//   return store.state.user?.loginUser == null;
// });

// 登录按钮点击事件处理
const handleLogin = () => {
  router.push("/user/login");
};

// 退出按钮点击事件处理
// const handleLogout = async () => {
//   try {
//     await axios.post("http://localhost:8101/api/user/logout");
//     store.dispatch("user/logout");
//     // 刷新
//     store.dispatch("user/getLoginUser", {
//       userName: "管理员",
//       userRole: ACCESS_ENUM.ADMIN,
//     });
//     store.state.user.loginUser = "未登录";
//     window.location.reload();
//   } catch (error) {
//     console.error("Logout failed", error);
//   }
// };

// 展示在菜单的路由数组
const visibleRoutes = computed(() => {
  return routes.filter((item, index) => {
    if (item.meta?.hideInMenu) {
      return false;
    }
    // 根据权限过滤菜单
    if (
        !checkAccess(store.state.user.loginUser, item?.meta?.access as string)
    ) {
      return false;
    }
    return true;
  });
});

// 默认主页
const selectedKeys = ref(["/"]);

// 路由跳转后，更新选中的菜单项
router.afterEach((to, from, failure) => {
  selectedKeys.value = [to.path];
});

console.log();

setTimeout(() => {
  store.dispatch("user/getLoginUser", {
    userName: "管理员",
    userRole: ACCESS_ENUM.ADMIN,
  });
}, 3000);

const doMenuClick = (key: string) => {
  router.push({
    path: key,
  });
};
</script>

<style scoped>
.title-bar {
  display: flex;
  align-items: center;
}

.title {
  font-size: 24px;
  font-weight: bold;
  color: #444;
  margin-left: 16px;
}

.logo {
  height: 48px;
}

.user-info {
  width: 250px;
  display: flex;
  align-items: center;
}

.user-info span {
  margin-right: 30px; /* 调整用户名和按钮之间的间距 */
}

</style>
