<template>

  <div class="container m-auto px-4 pt-5 duration-300">

    <div v-if="anime" class="mt-3 mb-10">
      <div v-if="anime.animeNo != 0">
        <div class="font-bold text-xl">{{anime.subject}}</div>
        <div class="text-sm mt-1">{{anime.originalSubject}}</div>

        <table class="mt-6 text-sm text-left text-gray-800 dark:text-gray-100 border-t border-gray-200 dark:border-zinc-700">
          <tbody>
          <tr v-if="anime.period" class="border-b border-gray-200 dark:border-zinc-700">
            <th class="py-4 px-6 font-medium text-gray-900 bg-gray-50 dark:text-white dark:bg-zinc-900/50">방영기간</th>
            <td class="py-4 px-6 break-all">{{anime.period}}</td>
          </tr>
          <tr class="border-b border-gray-200 dark:border-zinc-700">
            <th class="py-4 px-6 font-medium text-gray-900 bg-gray-50 dark:text-white dark:bg-zinc-900/50">방영상태</th>
            <td class="py-4 px-6 break-all">
              <span v-if="anime.status == 'ON' && anime.pureWeek"> 매주 ({{anime.weekText}}) {{anime.timeText}}</span>
              <span v-else-if="anime.status == 'ON' && !anime.pureWeek">{{anime.statusText}} ({{anime.weekText}})</span>
              <span v-else>{{anime.statusText}}</span>
            </td>
          </tr>
          <tr class="border-b border-gray-200 dark:border-zinc-700">
            <th class="py-4 px-6 font-medium text-gray-900 bg-gray-50 dark:text-white dark:bg-zinc-900/50">장르</th>
            <td class="pt-0 pb-2 px-6">
              <span class="rounded-md inline-block mb-0 ml-0 mt-2 mr-2 px-[8px] py-[5px] border border-zinc-300 dark:bg-zinc-900/50 dark:border-zinc-700 shadow-sm" v-for="tag in anime.genres.split(/,/g)" :key="tag"><router-link :to="`/anime?q=%23${encodeURIComponent(tag)}`">{{tag}}</router-link></span>
            </td>
          </tr>
          <tr v-if="anime.website" class="border-b border-gray-200 dark:border-zinc-700">
            <th class="py-4 px-6 font-medium text-gray-900 bg-gray-50 dark:text-white dark:bg-zinc-900/50">웹사이트</th>
            <td class="py-4 px-6 break-all"><a :href="anime.website" target="_blank">{{anime.website}}</a></td>
          </tr>
          <tr v-if="anime.twitter" class="border-b border-gray-200 dark:border-zinc-700">
            <th class="py-4 px-6 font-medium text-gray-900 bg-gray-50 dark:text-white dark:bg-zinc-900/50">트위터</th>
            <td class="py-4 px-6 break-all"><a :href="anime.twitter" target="_blank">{{anime.twitter}}</a></td>
          </tr>
          </tbody>
        </table>

        <div v-if="anime.captions.length" class="mt-6">
          <div class="text-md font-bold">자막정보</div>
          <table class="mt-3 text-sm text-left text-gray-800 dark:text-gray-100">
            <thead class="text-xs text-gray-700 uppercase dark:text-gray-400">
            <tr class="dark:text-gray-400 border-b border-gray-200 dark:border-zinc-700">
              <th scope="col" class="py-3 px-6 bg-gray-50 dark:bg-zinc-900/50">자막</th>
              <th scope="col" class="py-3 px-6 bg-gray-50 dark:bg-zinc-900/50">제작자</th>
              <th scope="col" class="py-3 px-6 bg-gray-50 dark:bg-zinc-900/50">날짜</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="caption in anime.captions" :key="caption.name" class="border-b border-gray-200 dark:border-zinc-700">
              <td class="py-4 px-6">
                <span v-if="caption.website"><a :href="caption.website" target="_blank" >{{caption.episodeText}}</a></span>
                <span v-else>준비중</span>
              </td>
              <td class="py-4 px-6"><router-link :to="`/anime?q=%40${encodeURIComponent(caption.name)}`">{{caption.name}}</router-link></td>
              <td class="py-4 px-6">{{caption.updDtText}}</td>
            </tr>
            </tbody>
          </table>
        </div>
      </div>
      <div v-else>
        <div class="text-xl text-center my-32">
          존재하지 않거나 삭제된 애니메이션 입니다.
        </div>
      </div>

    </div>

    <div class="flex-col justify-center relative text-gray-500">

      <label for="default-search" class="mb-2 text-sm font-medium text-gray-900 sr-only dark:text-gray-300">Search</label>
      <div class="relative">
        <div class="flex absolute inset-y-0 left-0 items-center pl-3 pointer-events-none">
          <i class="fa-solid fa-magnifying-glass"></i>
        </div>
        <input type="search" id="default-search" autocomplete="off" class="outline-none block p-4 pl-10 w-full text-sm text-gray-900 bg-gray-50 rounded-md border border-gray-300 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white" placeholder="애니메이션 검색 : 검색어 #장르 @제작자 /완결"  v-model="query" @click="autocorrectOn = true" @keydown="keyAutocorrect" @keyup="loadAutocorrect">
        <button type="button" @click="searchAnime()" class="text-white absolute right-2.5 bottom-2.5 bg-gray-600 hover:bg-gray-700 outline-none font-medium rounded-md text-sm px-4 py-2 dark:bg-zinc-800 dark:hover:bg-zinc-900">검색</button>
      </div>

      <div v-if="autocorrectOn && autocorrect.length">
        <ul class="autocorrect-list bg-white text-sky-600 dark:bg-zinc-900 dark:text-sky-300 mt-2 py-1 rounded-md border border-gray-300 dark:border-zinc-600 font-light">
          <li v-for="(node, i) in autocorrect" :class="autocorrectIndex != i ? 'grayscale-[100%]' : ''" class=" pl-8 pr-2 py-1 relative cursor-pointer" @mouseover="autocorrectIndex = i">
            <router-link :to="`/anime?animeNo=${node.key}`" class="block">
              <i :class="autocorrectIndex != i ? 'opacity-[0%]' : ''" class="fa-solid fa-arrow-right-long absolute w-4 h-4 left-2 top-2"></i>
              <span v-html="node.hl"></span>
            </router-link>
          </li>
        </ul>
      </div>

      <div v-if="!list.empty" class="mt-4">
        <div class="text-right mt-3 text-sm">
          총 <b>{{list.totalElements}}</b> 작품
        </div>
        <div class="mt-3 grid gap-4 md:grid-cols-2 xl:grid-cols-3">
          <div v-for="(node, i) in list.content" class="p-4 bg-white rounded-md border border-zinc-300 dark:bg-zinc-900/50 dark:border-zinc-700 shadow-sm">
            <div>
              <router-link :to="toAnimeViewUrl(node.animeNo)">
                <div class="text-md font-bold text-gray-800 dark:text-gray-300">{{node.subject}}</div>
                <div class="text-sm mt-1" v-if="node.originalSubject">{{node.originalSubject}}</div>
              </router-link>
            </div>
            <div class="mt-1 space-x-1 space-y-2 text-gray-800 dark:text-gray-300">
              <span class="rounded-md inline-block px-[5px] py-[3px] text-xs border border-zinc-300 dark:bg-zinc-900/50 dark:border-zinc-700 shadow-sm" v-for="tag in node.tags" :key="tag">{{tag}}</span>
              <span class="rounded-md inline-block px-[5px] py-[3px] text-xs border border-zinc-300 dark:bg-zinc-900/50 dark:border-zinc-700 shadow-sm" v-for="tag in node.genres.split(/,/g)" :key="tag"><router-link :to="`/anime?q=%23${encodeURIComponent(tag)}`">{{tag}}</router-link></span>
              <span class="rounded-md inline-block px-[5px] py-[3px] text-xs border border-zinc-300 dark:bg-zinc-900/50 dark:border-zinc-700 shadow-sm" v-if="node.website"><a :href="node.website" target="_blank" class="fas fa-home"></a></span>
              <span class="rounded-md inline-block px-[5px] py-[3px] text-xs border border-zinc-300 dark:bg-zinc-900/50 dark:border-zinc-700 shadow-sm" v-if="node.twitter"><a :href="node.twitter" target="_blank" class="fa-brands fa-twitter"></a></span>
              <span class="rounded-md inline-block px-[5px] py-[3px] text-xs border border-zinc-300 dark:bg-zinc-900/50 dark:border-zinc-700 shadow-sm" v-if="node.captionCount"><span class="fas fa-closed-captioning">&nbsp; {{node.captionCount}}</span></span>
            </div>
          </div>
        </div>
      </div>
      <div v-else>
        <div class="text-xl text-center my-32">
          <b>{{getNowSearchedQuery()}}</b>에 대한 검색결과가 없습니다.
        </div>
      </div>


    </div>

  </div>

</template>

<script setup lang="ts">
import {nextTick, onMounted, onUnmounted, Ref, ref} from "vue";
import PageData from "../../common/PageData";
import Anime from "../../domain/anime/Anime";
import animeRemote from "../../domain/anime/remote/animeRemote";
import AnimeAutocorrect from "../../domain/anime/AnimeAutocorrect";
import {onBeforeRouteUpdate, useRouter} from "vue-router";
import {Locate} from "../../common/Locate";
import scrollLoader from "../../common/ScrollLoader";

let list = ref(PageData.empty()) as Ref<PageData<Anime>>;
let anime = ref(null) as Ref<Anime|null>;
let lastAnimeNo = -1;
let page = ref(0);
let query = ref<string>(new Locate().getParameter('q', '') as string);
let autocorrect = ref([]) as Ref<AnimeAutocorrect[]>;
let autocorrectOn = ref(false);
let autocorrectIndex = ref(-1);
let autocorrectQuery = '';
let router = useRouter();
let sl = scrollLoader();

function load(locate: Locate = new Locate()) {
  autocorrectOn.value = false;
  const q = locate.getParameter('q', '')!!;
  if (query.value != q) {
    page.value = 0;
  }
  query.value = q;
  loadAnime(locate);
  loadList();
}

function loadAnime(locate: Locate) {
  const animeNo = locate.getIntParameter('animeNo', -1);
  if (animeNo > 0) {
    if (lastAnimeNo != animeNo) {
      lastAnimeNo = animeNo;
      animeRemote.getAnime(animeNo, node => anime.value = node);
    }
  } else {
    lastAnimeNo = -1;
    anime.value = null;
  }
}

function loadList() {
  const isFirstPage = page.value == 0;

  animeRemote.getAnimeList(page.value, query.value, pageData => {
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

function searchAnime() {
  page.value = 0;
  autocorrectOn.value = false;
  router.push(`/anime?q=${query.value}`);
}

function loadAutocorrect(event: KeyboardEvent) {
  const word = (event.target as any).value;
  if (autocorrectQuery != word) {
    autocorrectOn.value = true;
    autocorrectIndex.value = -1;
    autocorrectQuery = word;
    animeRemote.getAnimeListAutocorrect(word, list => autocorrect.value = list);
  }
}

function keyAutocorrect(event: KeyboardEvent) {
  const key = event.key;
  const len = autocorrect.value.length;

  switch (key) {
    case 'ArrowUp':
      if (len) {
        event.preventDefault();
        if (autocorrectIndex.value == -1) { autocorrectIndex.value = len -1; } else { autocorrectIndex.value--; }
      }
      return;
    case 'ArrowDown':
      if (len) {
        event.preventDefault();
        if (autocorrectIndex.value >= (len -1)) { autocorrectIndex.value = -1; } else { autocorrectIndex.value++; }
      }
      return;
    case 'Enter':
      if (!autocorrect.value.length || autocorrectIndex.value == -1) {
        searchAnime();
      } else {
        router.push(`/anime?animeNo=${autocorrect.value[autocorrectIndex.value].key}`)
      }
      return;
  }
}

function toAnimeViewUrl(animeNo: number) {
  return new Locate().setParameter('animeNo', animeNo+'').fullPath
}

function getNowSearchedQuery() {
  return new Locate().getParameter('q', '');
}

sl.callback(() => {
  page.value++;
  loadList();
});
load();

onBeforeRouteUpdate((to, from, next) => {
  load(new Locate(to.fullPath));
  next();
});

onUnmounted(() => {
  sl.destroy();
});
</script>
