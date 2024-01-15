<template>
  <div v-if="!!profile">
    <div v-if="state === 0">
      Loading...
    </div>
    <div v-if="state === 1">
      <div class="w-full px-4">
        <form class="bg-white shadow-lg rounded px-8 pt-6 pb-8 mb-4">
          <div class="mb-4 text-left">
            <label class="block text-gray-700 text-sm font-bold mb-2" for="Invite Code">
              ชื่อ
            </label>
            <label>{{ profile.displayName || '-' }}</label>
          </div>
          <div class="mb-4 text-left">
            <label class="block text-gray-700 text-sm font-bold mb-2" for="plate">
              ป้ายทะเบียนรถ
            </label>
            <input
              class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              id="plate" type="text" placeholder="ป้ายทะเบียนรถ" v-model="model.plate" maxlength="10">
          </div>
          <div class="mb-4 text-left">
            <label class="block text-gray-700 text-sm font-bold mb-2" for="company">
              บริษัทที่สังกัด
            </label>
            <input
              class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              id="company" type="text" placeholder="ชื่อบริษัท" v-model="model.company" maxlength="200">
          </div>
          <!-- <div class="mb-4 text-left">
            <label class="block text-gray-700 text-sm font-bold mb-2" for="Company">
              Company
            </label>
            <label>{{ company || '-' }}</label>
          </div> -->

          <div class="flex items-center justify-between">
            <button @click="submit"
              class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
              type="button">
              Submit
            </button>

          </div>
        </form>
      </div>

    </div>
    <div v-if="state === 2">
      2
    </div>
  </div>
  <div v-else>
    Loading...
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import liff from "@line/liff";
import axios from 'axios';
export default defineComponent({
  data() {
    return {
      message: "",
      error: "",
      isLoggedIn: false,
      accessToken: '',
      profile: {
        displayName: ''
      },
      model: {
        plate: '',
        company: ''
      },
      state: 1
    };
  },
  methods: {
    submit: () => {
      alert('ok')
    }
  },
  mounted() {
    liff
      .init({
        liffId: '2002732898-9A4RBWMJ',//import.meta.env.VITE_LIFF_ID,
         withLoginOnExternalBrowser: true
      })
      .then(async () => {
        this.message = "LIFF init succeeded.";
        try {
          this.isLoggedIn = liff.isLoggedIn();
          this.profile = await liff.getProfile()
          this.accessToken = liff.getAccessToken() ?? '';
          axios
            .post('https://lxt44sg1-7204.asse.devtunnels.ms/api/UserInfo', {
              accessToken: this.accessToken
            })
            .then(response => {
              console.log(response.data)
              this.profile = response.data;
            })
        }
        catch (e: any) {
          console.log(e)
        }
      })
      .catch((e: Error) => {
        this.message = "LIFF init failed.";
        this.error = `${e}`;
      });
  }
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
 
  color: #2c3e50;
  margin-top: 60px;
}
</style>
