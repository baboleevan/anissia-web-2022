<template>

  <div>

    <div v-if="isAdminMode">
      <div class="pt-2 pb-4">
        <input type="text" name="query" v-model="query" @keyup.enter="doQuery" autocomplete="off" placeholder="운영기록작성 /도움말" class="shadow-sm outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md block w-full p-4 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white">
      </div>

      <div v-if="openHelp" class="border border-gray-300 text-gray-900 text-sm leading-[2] rounded-md block w-full p-3 mt-1 mb-3 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white ">
        명령어 도움말<br/>
        <b>/권한반납 닉네임</b> - 자막제작자 권한을 반납합니다.
      </div>

      <div v-if="translatorApplyCount" class="py-3 px-1 text-sm leading-[1.8] border-b border-gray-300 dark:border-zinc-700">
        <router-link to="/translator/apply">현재 <b>{{translatorApplyCount}}</b> 건의 자막제작자 권한요청이 있습니다.</router-link>
      </div>
    </div>

    <div>
      <div v-for="(node, idx) in list.content" :key="node.apNo" class="py-3 px-1 text-sm leading-[1.8] border-b border-gray-300 dark:border-zinc-700 anissia-home-reduce-10">
        <div class="float-right pl-4 inline-block opacity-60">{{node.regDtText}}</div>
        <div v-if="node.code == 'TEXT'" v-html="node.html" :class="({'opacity-50': !node.published})"></div>
        <div v-else-if="node.code == 'ANIME'">
          <div>
            <span v-html="node.html"></span>
            <span v-if="node.hasDetail && !node.openDetail"> - <b @click="node.openDetail = true" class="cursor-pointer ml-1">자세히</b></span>
          </div>
          <div v-if="node.openDetail">
            <table class="mt-3 text-sm text-left text-gray-800 dark:text-gray-100">
              <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-zinc-700 dark:text-gray-400">
              <tr>
                <th scope="col" class="py-3 px-6">속성</th>
                <th scope="col" class="py-3 px-6">기존</th>
                <th scope="col" class="py-3 px-6">변경</th>
              </tr>
              </thead>
              <tbody>
              <tr v-for="item in node.codeAnimeChangedList" :key="item.nm" class="bg-white border-b dark:bg-zinc-900/50 dark:border-zinc-700">
                <th scope="row" class="py-4 px-6 font-medium text-gray-900 break-all dark:text-white">{{item.nm}}</th>
                <td class="py-4 px-6 break-all" :class="({'opacity-30': (item.pv == '-')})">{{item.pv}}</td>
                <td class="py-4 px-6 break-all">{{item.nv}}</td>
              </tr>
              </tbody>
            </table>
          </div>
        </div>
        <div v-else>
          {{node.code}} 는 지원하지 않는 활동 패널 코드입니다.
        </div>

      </div>
    </div>
  </div>



</template>

<script setup lang="ts">
import {nextTick, onMounted, onUnmounted, Ref, ref} from "vue";
import scrollLoader from "../../common/ScrollLoader";
import activePanelRemote from "./remote/activePanelRemote";
import PageData from "../../common/PageData";
import ActivePanelListItem from "./ActivePanelListItem";
import translatorRemote from "../translator/remote/translatorRemote";

const props = defineProps({
  // admin, public
  mode: String
});

const page = ref(0);
const sl = scrollLoader();
const list = ref(PageData.empty()) as Ref<PageData<ActivePanelListItem>>;
const query = ref('');
const openHelp = ref(false);
const translatorApplyCount = ref(0);
const isAdminMode = ref(false);

function loadTranslatorApplyCount() {
  translatorRemote.getAdminTranslatorApplyCount(count => translatorApplyCount.value = count);
}

function load() {
  const isFirstPage = page.value == 0;

  activePanelRemote.getList(page.value, props.mode!!, pageData => {
    if (isFirstPage) {
      list.value = pageData;
    } else {
      list.value = list.value.merge(pageData);
    }
    if (!pageData.last) {
      nextTick(() => sl.next())
    }
  });
}

function doQuery() {
  const line = query.value;
  query.value = '';

  if (line.trim() == '/도움말') {
    openHelp.value = true;
    return;
  }

  activePanelRemote.addNotice(line, result => {
    if (result.st == 'OK') {
      page.value = 0;
      load();
    } else {
      alert(result.msg);
    }
  });
}

isAdminMode.value = props.mode == 'admin';
if (isAdminMode.value) {
  sl.callback(() => {
    page.value++;
    load();
  });
  loadTranslatorApplyCount();
}
load();

onUnmounted(() => {
  sl.destroy();
});

</script>

<style>

</style>