<template>

  <div class="bg-neutral-100 dark:bg-zinc-800 min-h-screen text-zinc-800 dark:text-zinc-300 pb-10 box-border duration-300">

    <!-- 헤더 -->
    <header class="sticky  top-0 z-30 w-full backdrop-blur transition-colors lg:border-b lg:border-slate-900/10 dark:border-slate-50/[0.06] bg-white/80 dark:bg-zinc-900/70 flex">
      <div class="container m-auto flex justify-between items-center duration-300">
        <router-link to="/" class="block font-bold text-xl text-sky-700 dark:text-gray-300 p-4">ANISSIA</router-link>
        <div class="text-sm leading-6 font-semibold text-neutral-700 dark:text-gray-400 flex justify-end items-center">
          <router-link to="/schedule" class="hidden md:inline hover:text-sky-700 dark:hover:text-gray-200 p-4">
            <i class="fa-regular fa-calendar mr-1.5"></i> 편성표
          </router-link>
          <router-link to="/anime" class="hidden md:inline hover:text-sky-700 dark:hover:text-gray-200 p-4">
            <i class="fa-solid fa-film mr-1.5"></i> 애니
          </router-link>
          <router-link to="/inquiry" class="hidden md:inline hover:text-sky-700 dark:hover:text-gray-200 p-4">
            <i class="fa-regular fa-comments mr-1.5"></i> 질문답변
          </router-link>
          <router-link v-if="user.isAdmin && !url('/admin')" to="/admin" class="text-lg hover:text-sky-700 dark:hover:text-gray-200 p-4">
            <i class="fa-solid fa-sliders"></i>
          </router-link>
          <button class="text-lg hover:text-sky-700 dark:hover:text-gray-200 p-4 " @click="toggleTheme()">
            <span class="dark:hidden">
              <i class="fa-solid fa-sun"></i>
            </span>
            <span class="hidden dark:inline">
              <i class="fa-solid fa-moon"></i>
            </span>
          </button>
          <button class="text-lg hover:text-sky-700 dark:hover:text-gray-200 p-4" @click="onHeaderMenu = true">
            <i v-if="user.isLogin" class="fa-solid fa-user"></i>
            <i v-else class="fa-regular fa-user"></i>
          </button>
        </div>
      </div>
    </header>

    <div class="pop-close z-40 fixed h-screen w-screen top-0 left-0 bg-gray-800/10" :class="({'hidden': !onHeaderMenu})" @click="doCloseHeaderMenu">
      <div class="container m-auto flex justify-end">
        <div class="pop-not-close mt-12 mr-1.5 bg-zinc-50/90 backdrop-blur-sm rounded-md shadow-lg p-6 text-base font-semibold text-zinc-700 dark:bg-zinc-900/90 dark:text-gray-400 dark:highlight-white/5">
          <div class="mr-2">
            <ul class="space-y-6 mt-1">
              <li v-if="!user.isLogin"><router-link to="/login" class="pop-close hover:opacity-80"><i class="fa-solid fa-right-to-bracket mr-2.5"></i> 로그인</router-link></li>
              <li v-if="user.isLogin"><router-link to="/account" class="pop-close hover:opacity-80"><i class="fa-regular fa-address-card mr-2.5"></i> 회원정보</router-link></li>
              <li v-if="user.isLogin"><span @click="logout" class="pop-close hover:opacity-80 cursor-pointer"><i class="fa-solid fa-right-from-bracket mr-2.5"></i> 로그아웃</span></li>
              <li><router-link to="/schedule" class="pop-close hover:opacity-80"><i class="fa-regular fa-calendar mr-2.5"></i> 애니 편성표</router-link></li>
              <li><router-link to="/anime" class="pop-close hover:opacity-80"><i class="fa-solid fa-film mr-2.5"></i> 애니정보</router-link></li>
              <li><router-link to="/translator/apply" class="pop-close hover:opacity-80"><i class="fa-solid fa-signature mr-2.5"></i> 자막제작자 신청</router-link></li>
              <li><router-link to="/introduce" class="pop-close hover:opacity-80"><i class="fa-regular fa-star mr-2.5"></i> 애니시아 소개</router-link></li>
              <li><router-link to="/notice" class="pop-close hover:opacity-80"><i class="fa-regular fa-flag mr-2.5"></i> 공지사항</router-link></li>
              <li><router-link to="/inquiry" class="pop-close hover:opacity-80"><i class="fa-regular fa-comments mr-2.5"></i> 질문답변</router-link></li>
            </ul>
          </div>

        </div>

      </div>
    </div>

    <router-view/>

  </div>



</template>

<script setup lang="ts">

import {computed, onMounted, ref} from "vue";
import theme from "../../common/theme";
import anissia from "../../common/anissia";
import {sessionStore} from "../../domain/session/sessionStore";
import {onBeforeRouteUpdate, useRoute, useRouter} from "vue-router";
import sessionService from "../../domain/session/remote/sessionService";
import '../../common/md.pcss';

const router = useRouter();
const route = useRoute();
const session = sessionStore();

let onHeaderMenu = ref(false)
let url = anissia.url;
let user = computed(() => session.user);

function logout() {
  sessionService.logout(() => sessionService.amendPathBySession(route.path, router));
}

function toggleTheme() {
  theme.toggle();

}
function isNotAdmin() {
  return !location.pathname.startsWith('/admin');
}

function doCloseHeaderMenu(event: Event) {
  if (onHeaderMenu.value == true) {
    const closet = (event.target as HTMLElement).closest(".pop-close,.pop-not-close");
    if (closet?.matches('.pop-close')) {
      onHeaderMenu.value = false;
    }
  }
}

sessionService.amendPathBySession(route.fullPath, router);

onBeforeRouteUpdate((to, from, next) => {
  sessionService.amendPathBySession(to.fullPath, router, next);
});

</script>

<style>
@import url(@fortawesome/fontawesome-free/css/all.min.css);
@import url(//fonts.googleapis.com/earlyaccess/notosanskr.css);
body { font-family: "Noto Sans KR", "Malgun Gothic", "Dotum"; }
</style>