<template>

  <div class="container m-auto px-4 duration-300">

    <div v-if="anime" class="mb-10">
      <div v-if="anime.animeNo >= 0">

        <table class="w-full text-sm text-left text-zinc-500 dark:text-zinc-400">
          <thead>
            <tr class="border-b dark:border-zinc-700">
              <th colspan="2" class="py-4 px-6 w-[20%] text-center">애니메이션 {{anime.animeNo != 0 ? '편집' : '신규등록'}}</th>
            </tr>
          </thead>
          <tbody>
            <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
              <th class="py-4 px-6 w-[20%] text-zinc-500 dark:text-zinc-400">제목</th>
              <td class="py-4 px-6">
                <input type="text" v-model="anime.subject" name="subject" placeholder="제목" class="outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 block w-full p-2.5 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300">
              </td>
            </tr>
            <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
              <th class="py-4 px-6 w-[20%] text-zinc-500 dark:text-zinc-400">원제</th>
              <td class="py-4 px-6">
                <input type="text" v-model="anime.originalSubject" name="originalSubject" placeholder="원제" class="outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 block w-full p-2.5 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300">
              </td>
            </tr>
            <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
              <th class="py-4 px-6 w-[20%] text-zinc-500 dark:text-zinc-400">장르</th>
              <td class="pt-4 pb-2 px-6">
                <div @click="toggleGenreOpen">
                  <div v-for="genre in anime.editGenres" :key="genre" class="mr-2 mb-2 px-2 py-1 cursor-pointer inline-block border text-gray-900 dark:text-gray-200 border-gray-600 dark:border-zinc-800">
                    {{genre}}
                  </div>
                  <div v-if="anime.editGenres.length == 0" class="mb-2 py-1 mt-[2px] inline-block cursor-pointer">
                    선택된 장르가 없습니다.
                  </div>
                </div>
                <div v-if="anime?.editGenresOpen" class="mt-3">
                  <div v-for="genre in genres" :key="genre" @click="toggleGenre(genre)" class="mr-2 mb-2 px-2 py-1 cursor-pointer inline-block border text-gray-900 dark:text-gray-200 border-gray-600 dark:border-zinc-800" :class="anime.editGenres.indexOf(genre) != -1 ? '' : 'opacity-50'">
                    {{genre}}
                  </div>
                </div>

              </td>
            </tr>
            <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
              <th class="py-4 px-6 w-[20%] text-zinc-500 dark:text-zinc-400">상태</th>
              <td class="pt-4 pb-2 px-6">
                <label class="mr-2 mb-2 px-2 py-1 cursor-pointer inline-block border text-gray-900 dark:text-gray-200 border-gray-600 dark:border-zinc-800" :class="anime.status == 'ON' ? '' : 'opacity-50'">
                  <input type="radio" name="status" v-model="anime.status" value="ON" class="hidden" />편성표
                </label>
                <label class="mr-2 mb-2 px-2 py-1 cursor-pointer inline-block border text-gray-900 dark:text-gray-200 border-gray-600 dark:border-zinc-800" :class="anime.status == 'OFF' ? '' : 'opacity-50'">
                  <input type="radio" name="status" v-model="anime.status" value="OFF" class="hidden" />편성표-결방
                </label>
                <label class="mr-2 mb-2 px-2 py-1 cursor-pointer inline-block border text-gray-900 dark:text-gray-200 border-gray-600 dark:border-zinc-800" :class="anime.status == 'END' ? '' : 'opacity-50'">
                  <input type="radio" name="status" v-model="anime.status" value="END" class="hidden" />완결
                </label>
              </td>
            </tr>
            <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
              <th class="py-4 px-6 w-[20%] text-zinc-500 dark:text-zinc-400">요일</th>
              <td class="pt-4 pb-2 px-6">
                <label v-for="(week, i) in weekList" :key="week" class="mr-2 mb-2 px-2 py-1 cursor-pointer inline-block border text-gray-900 dark:text-gray-200 border-gray-600 dark:border-zinc-800" :class="(i+'') == anime.week ? '' : 'opacity-50'">
                  <input type="radio" name="week" v-model="anime.week" :value="i+''" class="hidden" />
                  {{week}}
                </label>
              </td>
            </tr>
            <tr v-if="anime.week != '7'" class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
              <th class="py-4 px-6 w-[20%] text-zinc-500 dark:text-zinc-400">시간</th>
              <td class="py-4 px-6">
                <input type="time" v-model="anime.time" class="outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 block p-2.5 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300"/>
              </td>
            </tr>
            <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
              <th class="py-4 px-6 w-[20%] text-zinc-500 dark:text-zinc-400">시작일</th>
              <td class="py-4 px-6">
                <div>
                  <label v-for="dateType in dateTypeList" :key="dateType" class="mr-2 px-2 py-1 cursor-pointer inline-block border text-gray-900 dark:text-gray-200 border-gray-600 dark:border-zinc-800" :class="dateType == anime.editStartDateType ? '' : 'opacity-50'">
                    <input type="radio" name="startDate" v-model="anime.editStartDateType" :value="dateType" class="hidden" />
                    {{dateType}}
                  </label>
                </div>
                <div v-if="anime.editStartDateType != 'N/A'" class="mt-3 text-bold text-lg">
                  <span>
                    <input type="text" v-model="anime.editStartDateYear" maxlength="4" placeholder="YYYY" class="w-[50px] py-1 text-center outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300"> 년
                    <span v-if="anime.editStartDateType != 'Y'" class="ml-1">
                      <input type="text" v-model="anime.editStartDateMonth" maxlength="2" placeholder="MM" class="w-[32px] py-1 text-center outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300"> 월
                      <span v-if="anime.editStartDateType != 'YM'" class="ml-1"><input type="text" v-model="anime.editStartDateDate" maxlength="2" placeholder="DD" class="w-[32px] py-1 text-center outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300"> 일</span>
                    </span>
                  </span>
                </div>
              </td>
            </tr>
            <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
              <th class="py-4 px-6 w-[20%] text-zinc-500 dark:text-zinc-400">종료일</th>
              <td class="py-4 px-6">
                <div>
                  <label v-for="dateType in dateTypeList" :key="dateType" class="mr-2 px-2 py-1 cursor-pointer inline-block border text-gray-900 dark:text-gray-200 border-gray-600 dark:border-zinc-800" :class="dateType == anime.editEndDateType ? '' : 'opacity-50'">
                    <input type="radio" name="endDate" v-model="anime.editEndDateType" :value="dateType" class="hidden" />
                    {{dateType}}
                  </label>
                </div>
                <div v-if="anime.editEndDateType != 'N/A'" class="mt-3 text-bold text-lg">
                  <span>
                    <input type="text" v-model="anime.editEndDateYear" maxlength="4" placeholder="YYYY" class="w-[50px] py-1 text-center outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300"> 년
                    <span v-if="anime.editEndDateType != 'Y'" class="ml-1">
                      <input type="text" v-model="anime.editEndDateMonth" maxlength="2" placeholder="MM" class="w-[32px] py-1 text-center outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300"> 월
                      <span v-if="anime.editEndDateType != 'YM'" class="ml-1"><input type="text" v-model="anime.editEndDateDate" maxlength="2" placeholder="DD" class="w-[32px] py-1 text-center outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300"> 일</span>
                    </span>
                  </span>
                </div>
              </td>
            </tr>
            <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
              <th class="py-4 px-6 w-[20%] text-zinc-500 dark:text-zinc-400">웹사이트</th>
              <td class="py-4 px-6">
                <input type="text" v-model="anime.website" name="website" placeholder="웹사이트" class="outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 block w-full p-2.5 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300">
              </td>
            </tr>
            <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
              <th class="py-4 px-6 w-[20%] text-zinc-500 dark:text-zinc-400">트위터</th>
              <td class="py-4 px-6">
                <input type="text" v-model="anime.twitter" name="twitter" placeholder="트위터" class="outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 block w-full p-2.5 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300">
              </td>
            </tr>
            <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
              <th class="py-4 px-6 w-[20%] text-zinc-500 dark:text-zinc-400">자막참여자</th>
              <td class="py-4 px-6">
                <span v-for="cp in anime.captions" :key="cp.name" class="mr-4">{{cp.name}}</span>
                <input type="button" value="자막참여" @click="addCaption()" class="text-white bg-gray-600 hover:bg-gray-700 outline-none font-medium rounded-md text-sm px-4 py-2 dark:bg-zinc-800 dark:hover:bg-zinc-700" />
              </td>
            </tr>
          </tbody>
        </table>
        <div class="mt-4 overflow-auto">
          <div class="float-left">
            <input type="button" value="삭제" @click="doDelete()" class="text-white bg-gray-600 hover:bg-gray-700 outline-none font-medium rounded-md text-sm px-4 py-2 dark:bg-zinc-900/50 dark:hover:bg-zinc-700" />
          </div>
          <div class="float-right">
            <input type="button" value="저장" @click="doSave()" class="text-white bg-rose-700 hover:bg-rose-800 outline-none font-medium rounded-md text-sm px-4 py-2 dark:bg-red-800 dark:hover:bg-red-700" />
          </div>
        </div>


      </div>
      <div v-else>
        <div class="text-xl text-center my-32">
          존재하지 않거나 삭제된 애니메이션 입니다.
        </div>
      </div>

    </div>

    <div class="flex w-full justify-between rounded-md shadow-sm mt-1">
      <router-link to="/admin/anime" class="text-center flex-1 rounded-l-lg p-4 text-sm border border-gray-200 dark:border-zinc-700 dark:bg-zinc-900" :class="state === 'list' ? 'bg-white text-blue-700 dark:text-gray-300' : 'bg-gray-50 text-gray-500 dark:grayscale-[50%] dark:text-neutral-500 dark:bg-opacity-50'">
        전체
      </router-link>
      <router-link to="/admin/anime?state=delist" class="text-center flex-1 rounded-r-lg p-4 text-sm border border-gray-200 dark:border-zinc-700 dark:bg-zinc-900" :class="state === 'delist' ? 'bg-white text-blue-700 dark:text-gray-300' : 'bg-gray-50 text-gray-500 dark:grayscale-[50%] dark:text-neutral-500 dark:bg-opacity-50'">
        삭제대기
      </router-link>
    </div>

    <div class="flex-col justify-center relative text-gray-500 mt-4">

      <div v-if="state == 'list'" class="relative">
        <div class="flex absolute inset-y-0 left-0 items-center pl-3 pointer-events-none">
          <i class="fa-solid fa-magnifying-glass"></i>
        </div>
        <input type="search" id="default-search" autocomplete="off" class="outline-none block p-4 pl-10 w-full text-sm text-gray-900 bg-gray-50 rounded-md border border-gray-300 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white" placeholder="애니메이션 검색 : 검색어 #장르 @제작자 /완결"  v-model="query" @click="autocorrectOn = true" @keydown="keyAutocorrect" @keyup="loadAutocorrect">
        <router-link to="/admin/anime?animeNo=0" type="button" class="text-white absolute right-2.5 bottom-2.5 bg-gray-600 hover:bg-gray-700 outline-none font-medium rounded-md text-sm px-4 py-2 dark:bg-zinc-800 dark:hover:bg-zinc-900" >신규</router-link>
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
              <router-link v-if="!node.agendaNo" :to="toAnimeViewUrl(node.animeNo)">
                <div class="text-md font-bold text-gray-800 dark:text-gray-300">{{node.subject}}</div>
                <div class="text-sm mt-1" v-if="node.originalSubject">{{node.originalSubject}}</div>
              </router-link>
              <div v-else>
                <input type="button" value="복원" @click="doRecover(node)" class="float-right text-white bg-rose-700 hover:bg-rose-800 outline-none font-medium rounded-md text-sm px-4 py-2 dark:bg-red-800 dark:hover:bg-red-700" />
                <div class="text-md font-bold text-gray-800 dark:text-gray-300">{{node.subject}}</div>
                <div class="text-sm mt-1" v-if="node.originalSubject">{{node.originalSubject}}</div>
              </div>
            </div>
            <div class="mt-1 space-x-1 space-y-2 text-gray-800 dark:text-gray-300">
              <span class="rounded-md inline-block px-[5px] py-[3px] text-xs border border-zinc-300 dark:bg-zinc-900/50 dark:border-zinc-700 shadow-sm" v-for="tag in node.tags" :key="tag">{{tag}}</span>
              <span class="rounded-md inline-block px-[5px] py-[3px] text-xs border border-zinc-300 dark:bg-zinc-900/50 dark:border-zinc-700 shadow-sm" v-for="tag in node.genres.split(/,/g)" :key="tag"><router-link :to="`/admin/anime?q=%23${encodeURIComponent(tag)}`">{{tag}}</router-link></span>
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
import anissia from "../../common/anissia";

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
let weekList = ref(['日', '月', '火', '水', '木', '金', '土', '外', '新']);
let dateTypeList = ref(['YMD', 'YM', 'Y', 'N/A']);
const genres = ref([]) as Ref<string[]>;
const state = ref('list');

function init() {
  animeRemote.getGenres(_genres => genres.value = _genres)
}

function clear(locate: Locate) {
  state.value = locate.getParameter('state', 'list') == 'list' ? 'list' : 'delist';
  page.value = 0;
}

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

function loadAnime(locate: Locate, forced: boolean = false) {
  if (forced) {
    lastAnimeNo = -1;
  }
  const animeNo = locate.getIntParameter('animeNo', -1);
  if (animeNo > 0) {
    if (lastAnimeNo != animeNo) {
      lastAnimeNo = animeNo;
      animeRemote.getAnime(animeNo, node => anime.value = node.bindEdit());
    }
  } else if (animeNo == 0) {
    lastAnimeNo = -1;
    anime.value = new Anime().bindEdit();
  } else {
    lastAnimeNo = -1;
    anime.value = null;
  }
}

function loadList() {
  const isFirstPage = page.value == 0;

  if (state.value === 'list') {
    animeRemote.getAdminAnimeList(query.value, page.value, pageData => {
      if (isFirstPage) {
        list.value = pageData;
      } else {
        list.value = list.value.merge(pageData);
      }
      if (!pageData.last) {
        nextTick(() => sl.next())
      }
    });
  } else if (state.value === 'delist') {
    animeRemote.getAdminAnimeDelist(pageData => {
      list.value = pageData;
    });
  }
}



function searchAnime() {
  page.value = 0;
  autocorrectOn.value = false;
  router.push(`/admin/anime?q=${query.value}`);
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

function toggleGenreOpen() {
  const ani = anime.value!!;
  ani.editGenresOpen = !ani.editGenresOpen;
}

function toggleGenre(genre: string) {
  const ani = anime.value!!;
  const genres = ani.editGenres;
  if (genres.indexOf(genre) != -1) {
    ani.editGenres = ani.editGenres.filter(e => e != genre);
  } else {
    if (genres.length >= 3) {
      ani.editGenres = [...ani.editGenres.slice(1, 3), genre];
    } else {
      ani.editGenres = [...ani.editGenres, genre];
    }
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
        router.push(`/admin/anime?animeNo=${autocorrect.value[autocorrectIndex.value].key}`)
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

function addCaption() {
  animeRemote.addAdminCaption(anime.value?.animeNo!!, result => {
    if (result.st == 'OK') {
      loadAnime(new Locate(), true);
      alert("자막제작자로 참여하셨습니다.");
    } else if (result.msg) {
      alert(result.msg);
    }
  });
}

function doDelete() {
  if (confirm(`${anime.value?.subject}을(를) 삭제하시겠습니까?\n임의삭제시 권한박탈의 사유가됩니다.`)) {
    animeRemote.deleteAdminAnime(anime.value?.animeNo!!, result => {
      if (result.st == 'OK') {
        router.push(`/admin/anime`);
      } else if (result.msg) {
        alert(result.msg);
      }
    });
  }
}

function doRecover(anime: Anime) {
  if (confirm(`${anime.subject}을(를) 복원하시겠습니까?\n임의조작시 권한박탈의 사유가됩니다.`)) {
    animeRemote.recoverAdminAnime(anime.agendaNo, result => {
      if (result.st == 'OK') {
        router.push(`/admin/anime?animeNo=${result.data}`);
      } else if (result.msg) {
        alert(result.msg);
      }
    });
  }
}

function doSave() {
  const ani = (anime.value!!).applyEdit();
  const isNew = ani.animeNo == 0;
  if (confirm(`${ani.subject}을(를) ${isNew ? '추가' : '수정'} 하시겠습니까?\n임의조작시 권한박탈의 사유가됩니다.`)) {

    if (!anissia.checkAnimeDate(ani.editStartDateType, ani.startDate)) {
      alert('시작일을 입력해주세요.');
      return;
    }
    if (!anissia.checkAnimeDate(ani.editEndDateType, ani.endDate)) {
      alert('종료일을 입력해주세요.');
      return;
    }

    if (isNew) {
      animeRemote.addAdminAnime(ani, result => {
        if (result.st == 'OK') {
          router.push(`/admin/anime?animeNo=${result.data}`)
          alert("애니메이션이 추가되었습니다.");
        } else if (result.msg) {
          alert(result.msg);
        }
      });
    } else {
      animeRemote.updateAdminAnime(ani, result => {
        if (result.st == 'OK') {
          loadAnime(new Locate(), true);
          alert("애니메이션이 수정되었습니다.");
        } else if (result.msg) {
          alert(result.msg);
        }
      });
    }
  }
}

init();
clear(new Locate());
sl.callback(() => {
  page.value++;
  loadList();
});
load();

onBeforeRouteUpdate((to, from, next) => {
  clear(new Locate(to.fullPath));
  load(new Locate(to.fullPath));
  next();
});

onUnmounted(() => {
  sl.destroy();
});
</script>
