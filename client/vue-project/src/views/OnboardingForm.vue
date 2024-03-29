<script setup lang="ts">
import TopNavbarVue from "../components/TopNavbar.vue";
import { ref } from "vue";
import {Auth} from 'aws-amplify';
import restaurantAPIService from '../services/restaurantAPI';
import { useRouter } from "vue-router";
import LoadingSpinner from './../components/LoadingSpinner.vue';
import countryList from '../countries'

const isLoading = ref(true);
const restName = ref("");
const street = ref("");
const city = ref("");
const zip = ref("");
const country = ref("");
const tables = ref("1");
const staff = ref(1);
const userData: any = ref(null);
const router = useRouter();

Auth.currentAuthenticatedUser().then((u)=>{
  const email = u.attributes.email;
  const username = u.username;
  const user = {email, username}
  userData.value = user;
  (async () => {
    const restaurant = await restaurantAPIService.getRestaurant(user.username);
    isLoading.value=false;
    if (restaurant.length > 0) router.push('/dashboard/overview');
})()
});


async function submitForm() {
  if(staff.value > 50 || Number(tables.value) > 50) {
    alert("You can't have more than 50 tables or staff members!");
    return;
  }
  const formInput = {
    restName: restName.value,
    street: street.value,
    city: city.value,
    zip: zip.value,
    country: country.value,
    tables: tables.value,
    staff: staff.value
  };
  const res = await restaurantAPIService.newRestaurant(formInput, userData.value.username)
  if(res.body.restName){
    router.push('/dashboard/overview')
  } else {
    alert("Something went wrong, please try again later.")
  }
}
</script>

<template>
  <TopNavbarVue></TopNavbarVue>
    <div v-if="isLoading">
    <LoadingSpinner/>
  </div>
  <div v-else id="main" class="h-full flex justify-center items-start mt-5">
    
    <div class=" mx-auto max-w-5xl flex flex-col justify-center items-center">
      <div class="shadow sm:overflow-hidden sm:rounded-md mb-4 mt-4 w-2/3">
        <div class="space-y-4 bg-violet-100 px-4 py-5 sm:p-6">
          <h1 class="text-2xl">Welcome to mytable!</h1>
          <h3 class="text-sm font-medium text-gray-700">
            You're almost ready to start.
          </h3>
          <p class="mt-2 text-sm text-gray-500">
            We'll need some more information to complete your onboarding.
          </p>
        </div>
      </div>

      <div  class="mt-10 sm:mt-0 w-2/3">
        <div class="md:grid md:grid-cols-6 md:gap-6">
          <div class="mt-5 md:col-span-6 md:col-start-1 md:mt-0">
            <form action="#" @submit.prevent="submitForm" method="POST">
              <div class="overflow-hidden shadow sm:rounded-md">
                <div class="bg-white px-4 py-5 sm:p-6">
                  <div class="grid grid-cols-6 gap-6">
                    <div class="col-span-6 sm:col-span-4">
                      <label
                        for="email-address"
                        class="block text-sm font-medium text-gray-700"
                        >Restaurant Name</label
                      >
                      <input
                        v-model="restName"
                        type="text"
                        required
                        name="restaurant-name"
                        id="restaurant-name"
                        class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm"
                      />
                    </div>
                   

                    <div class="col-span-6">
                      <label
                        for="street-address"
                        class="block text-sm font-medium text-gray-700"
                        >Street address</label
                      >
                      <input
                        v-model="street"
                        type="text"
                        required
                        name="street-address"
                        id="street-address"
                        autocomplete="street-address"
                        class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm"
                      />
                    </div>

                    <div class="col-span-6 sm:col-span-6 lg:col-span-2">
                      <label
                        for="city"
                        class="block text-sm font-medium text-gray-700"
                        >City</label
                      >
                      <input
                        v-model="city"
                        type="text"
                        required
                        name="city"
                        id="city"
                        autocomplete="address-level2"
                        class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm"
                      />
                    </div>

                    <div class="col-span-6 sm:col-span-3 lg:col-span-2">
                      <label
                        for="postal-code"
                        class="block text-sm font-medium text-gray-700"
                        >ZIP / Postal code</label
                      >
                      <input
                        v-model="zip"
                        type="text"
                        required
                        name="postal-code"
                        id="postal-code"
                        autocomplete="postal-code"
                        class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm"
                      />
                    </div>

                    <div class="col-span-6 sm:col-span-3 lg:col-span-2">
                      <label
                        for="country"
                        class="block text-sm font-medium text-gray-700"
                        >Country</label
                      >
                      <select
                        v-model="country"
                        id="country"
                        name="country"
                        autocomplete="country-name"
                        class="mt-1 block w-full rounded-md border border-gray-300 bg-white py-2 px-3 shadow-sm focus:border-indigo-500 focus:outline-none focus:ring-indigo-500 sm:text-sm"
                      >
                        <option v-for="item in countryList">{{item}}</option>
                      </select>
                    </div>

                    <div class="hidden sm:block col-span-6" aria-hidden="true">
                      <div class="py-5">
                        <div class="border-t border-gray-200" />
                      </div>
                    </div>

                    <div class="col-span-6 sm:col-span-3">
                      <label
                        for="country"
                        class="block text-sm font-medium text-gray-700"
                        >How many tables does your restaurant have?</label
                      >
                      <select
                        v-model="tables"
                        id="table-number"
                        name="table-number"
                        class="mt-1 block w-full rounded-md border border-gray-300 bg-white py-2 px-3 shadow-sm focus:border-indigo-500 focus:outline-none focus:ring-indigo-500 sm:text-sm"
                      >
                        <option v-for="num in 20" :key="num">{{ num }}</option>
                      </select>
                      <p class="mt-2 text-sm text-gray-500">
                        We'll preconfigure your QR codes.
                      </p>
                    </div>

                    <div class="col-span-6 sm:col-span-3">
                      <label
                        for="waiter-name"
                        class="block text-sm font-medium text-gray-700"
                        >How many waiters are there in your team? </label
                      >
                      <select
                        v-model="staff"
                        type="number"
                        name="waiter-name"
                        id="waiter-name"
                        autocomplete="waiter-name"
                        class="mt-1 block w-full rounded-md border border-gray-300 bg-white py-2 px-3 shadow-sm focus:border-indigo-500 focus:outline-none focus:ring-indigo-500 sm:text-sm">
                        
                        <option v-for="num in 30" :key="num">{{ num }}</option>
                      
                    </select>
                      <p class="mt-2 text-sm text-gray-500">
                        You will be able to edit
                        this later.
                      </p>
                      <div class="flex flex-row flex-wrap">
                      </div>
                    </div>
                  </div>
                </div>

                <div class="bg-gray-50 px-4 py-3 text-right sm:px-6">
                  <button
                    type="submit"
                    class="inline-flex justify-center rounded-md border border-transparent bg-violet-600 py-2 px-4 text-sm font-medium text-white shadow-sm hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2"
                  >
                    Save
                  </button>
                </div>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>

  <div>
  </div>
</div>
</template>
