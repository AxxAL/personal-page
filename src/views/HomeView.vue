<script lang="ts">
  import { defineComponent } from "vue";

  export default defineComponent({
    data() {
      return {
        time: "00:00:00",
        secretClicked: 0
      }
    },

    mounted() {
      this.setTime().then(() => setInterval(this.setTime, 1000));
    },

    methods: {
      async setTime() {
        this.time = new Date().toLocaleTimeString();
      }
    },

    watch: {
      secretClicked() {
        if (this.secretClicked === 5) {
          this.$router.push("/secret");
        }
      }
    }
  });
</script>

<template>
  <div class="h-screen flex flex-col px-4 py-8 sm:py-4 sm:items-center justify-start sm:justify-center">

    <div class="max-w-lg">
      <h1 class="text-white text-xl sm:text-4xl font-vcr animation-vcr">Salutations.</h1>
      <p class="text-white text-lg sm:text-lg font-vcr animation-vcr">I build software for the web.</p>
      <p class="text-white text-lg sm:text-lg font-vcr animation-vcr">
        The time is ticking <span @click="secretClicked++">{{ time }}</span>! Go check out my <span class="animation-vcr-glow"><a href="https://github.com/AxxAL" target="_blank">GitHub page</a></span>!
      </p>
      <p class="text-white text-lg sm:text-lg font-vcr animation-vcr">
        Or if you're in to art, check out the <span class="animation-vcr-glow"><router-link to="/art">art page</router-link></span>!
      </p>
    </div>

  </div>

  <img v-if="secretClicked >= 3" class="w-auto h-8 absolute bottom-0 left-8" src="../assets/images/dancing-banana.gif" alt="Animated pixel bananaman">
</template>