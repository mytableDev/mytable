<script setup lang="ts">
import { ref } from "vue";
import { Auth } from 'aws-amplify';

const show = ref(false);
const userData: any = ref(null)

Auth.currentAuthenticatedUser().then((u)=>{
  const email = u.attributes.email;
  const username = u.username;
  const user = {email, username}
  userData.value = user;
})

async function signOut() {
    try {
        await Auth.signOut();
        window.location.href = "/";
    } catch (error) {
        console.log('error signing out: ', error);
    }
}

</script>

<template>
  <div class="top-navbar">
  <nav class="bg-gray-100 border-b border-gray-200">
    <div class="mx-auto px-2 sm:px-6 lg:px-8">
      <div class="relative flex h-16 items-center justify-between">
        <div
          class="flex flex-1 items-center justify-center sm:items-stretch sm:justify-start"
        >
          <div class="flex flex-shrink-0 items-center"></div>
          <div class="hidden sm:ml-6 sm:block">
            <div class="flex space-x-4"></div>
          </div>
        </div>
        <div
          class="absolute inset-y-0 right-0 flex items-center pr-2 sm:static sm:inset-auto sm:ml-6 sm:pr-0"
        >
          <a
            @mouseover="show = true"
            href="#"
            class="bg-gray-100 border-gray-200 text-gray-900 px-3 py-2 rounded-md text-sm font-medium"
            aria-current="page"
            ><div id="email" v-if="(userData && userData.email)">{{userData.email}}</div></a
          >
          <div class="relative ml-3">
            <div
              @mouseover="show = true"
              @mouseleave="show = false"
              v-if="show"
              class="absolute right-0 z-10 mt-2 w-48 origin-top-right rounded-md bg-white py-1 shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none"
              role="menu"
              aria-orientation="vertical"
              aria-labelledby="user-menu-button"
              tabindex="-1"
            >
              <slot>
                <a
                  @click="signOut"
                  href="#"
                  class="block px-4 py-2 text-sm text-gray-700"
                  role="menuitem"
                  tabindex="-1"
                  id="user-menu-item-2"
                  >Sign out
                </a>
                </slot>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="sm:hidden" id="mobile-menu">
      <div class="space-y-1 px-2 pt-2 pb-3">
        <a
          href="#"
          class="bg-gray-900 text-white block px-3 py-2 rounded-md text-base font-medium"
          aria-current="page"
          >Dashboard</a
        >
      </div>
    </div>
  </nav>
</div>
</template>
