<template>

  <div class="container m-auto px-4 pt-5 duration-300">

    <div class="font-bold text-xl">회원 정보</div>

    <div class="overflow-x-auto relative mt-5">
      <table class="w-full text-sm text-left text-zinc-500 dark:text-zinc-400">
        <tbody>
        <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
          <th scope="row" class="py-4 px-6 font-medium w-[20%] text-zinc-500 dark:text-zinc-400">계정</th>
          <td class="py-4 px-6">
            <b class="text-md mr-3">{{account.email}}</b>
          </td>
        </tr>
        <tr class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
          <th scope="row" class="py-4 px-6 font-medium text-zinc-500 dark:text-zinc-400">권한</th>
          <td class="py-4 px-6">
            <span class="rounded-md inline-block px-[5px] py-[3px] text-xs border border-zinc-300 dark:bg-zinc-900/50 dark:border-zinc-700 shadow-sm" v-for="node in account.rolesText" :key="node">
              {{node}}
            </span>
          </td>
        </tr>
        <tr v-if="mode != 'edit-nickname'" class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
          <th scope="row" class="py-4 px-6 font-medium text-zinc-500 dark:text-zinc-400">닉네임</th>
          <td class="py-4 px-6">
            <b class="text-md mr-3">{{account.name}}</b>
            <button type="button" @click="mode = 'edit-nickname'" class="py-1 px-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-md border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 dark:bg-zinc-900/50 dark:text-gray-400 dark:border-zinc-700 dark:hover:text-white dark:hover:bg-zinc-900">수정</button>
          </td>
        </tr>
        <tr v-if="mode == 'edit-nickname'" class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
          <th scope="row" class="py-4 px-6 font-medium text-zinc-500 dark:text-zinc-400">닉네임</th>
          <td class="py-4 px-6">
            <div class="max-w-[300px]">
              <div>
                <input type="text" v-model="editNickname" name="nickname" class="outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 block w-full p-2.5 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300" placeholder="닉네임">
              </div>
              <div class="mt-3">
                <input type="password" v-model="editNicknamePassword" name="password" placeholder="암호" class="outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 block w-full p-2.5 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300">
              </div>
              <div class="mt-3">
                <button type="button" @click="updateNickname" class="w-full py-2 font-bold text-gray-900 focus:outline-none bg-white rounded-md border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 dark:bg-zinc-900/50 dark:text-gray-400 dark:border-zinc-700 dark:hover:text-white dark:hover:bg-zinc-900">수정</button>
              </div>
            </div>
          </td>
        </tr>
        <tr v-if="mode != 'edit-password'" class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
          <th scope="row" class="py-4 px-6 font-medium text-zinc-500 dark:text-zinc-400">암호</th>
          <td class="py-4 px-6">
            <button type="button" @click="mode = 'edit-password'" class="py-1 px-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-md border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 dark:bg-zinc-900/50 dark:text-gray-400 dark:border-zinc-700 dark:hover:text-white dark:hover:bg-zinc-900">변경하기</button>
          </td>
        </tr>
        <tr v-if="mode == 'edit-password'" class="bg-white border-b dark:bg-zinc-900 dark:border-zinc-700">
          <th scope="row" class="py-4 px-6 font-medium text-zinc-500 dark:text-zinc-400">암호</th>
          <td class="py-4 px-6">
            <div class="max-w-[300px]">
              <div>
                <input type="password" v-model="editPrevPassword" name="password" placeholder="기존 암호" class="outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 block w-full p-2.5 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300">
              </div>
              <div class="mt-3">
                <input type="password" v-model="editPassword" name="password1" placeholder="새 암호" class="outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 block w-full p-2.5 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300">
              </div>
              <div class="mt-3">
                <input type="password" v-model="editPasswordConfirm" name="password2" placeholder="새 암호 확인" class="outline-none bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-md focus:border-blue-600 block w-full p-2.5 dark:bg-zinc-700 dark:border-zinc-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-zinc-300">
              </div>
              <div class="mt-3">
                <button type="button" @click="updatePassword" class="w-full py-2 font-bold text-gray-900 focus:outline-none bg-white rounded-md border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 dark:bg-zinc-900/50 dark:text-gray-400 dark:border-zinc-700 dark:hover:text-white dark:hover:bg-zinc-900">암호변경</button>
              </div>
            </div>
          </td>
        </tr>
        </tbody>
      </table>
    </div>
  </div>

</template>

<script setup lang="ts">
import {ref} from "vue";
import accountRemote from "../../domain/account/remote/accountRemote";
import {Account} from "../../domain/account/Account";

const account = ref(new Account());

let mode = ref('none');

let editNickname = ref('');
let editNicknamePassword = ref('');

let editPrevPassword = ref('');
let editPassword = ref('');
let editPasswordConfirm = ref('');

function load() {
  mode.value = 'none';
  accountRemote.getAccount(e => {
    account.value = e;
    editNickname.value = e.name;
  });
}

function updateNickname() {
  accountRemote.updateUserName(editNickname.value, editNicknamePassword.value, e => {
    if (e.success) {
      load();
    } else {
      alert(e.msg || '닉네임 변경에 실패하였습니다.');
    }
  })
}

function updatePassword() {
  accountRemote.updateUserPassword(editPrevPassword.value, editPassword.value, editPasswordConfirm.value, e => {
    if (e.success) {
      load();
    } else {
      alert(e.msg || '암호변경에 실패하였습니다.');
    }
  });
}

load();
</script>
