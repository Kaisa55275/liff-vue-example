<template>
  <div class="hello-world" v-if="isInClient">
    <h1 class="hello-world__title">
      Welcome to Your Liff + Vue.js App
    </h1>
    <ul class="hello-world__profile" v-show="liffState.profile">
      <li class="profile-items" v-for="(v, k) in liffState.profile" :key="k">
        <img v-if="k === 'pictureUrl'" :src="v" alt="line-profile-picture" />
        <span v-else>{{ `${k}: ${v}` }}</span>
      </li>
    </ul>
  </div>
  <div
    class="hello-world--loading"
    v-else-if="isInClient === 'NOT_INITIALIZED'"
  >
    Loading...
  </div>
  <div class="hello-world--inactive" v-else-if="isInClient === false">
    Please open in LIFF browser!!
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, reactive, ref } from "vue";
import liff from "@line/liff";

type LiffState = {
  profile?: {
    userId: string;
    displayName: string;
    pictureUrl?: string;
    statusMessage?: string;
  };
};

export default defineComponent({
  setup() {
    const isInClient = ref<boolean | "NOT_INITIALIZED">("NOT_INITIALIZED");
    const liffState = reactive<LiffState>({
      profile: undefined
    });

    const getProfile = async () => {
      const profile = await liff.getProfile();
      liffState.profile = profile;
    };

    onMounted(async () => {
      await liff.init({ liffId: process.env.VUE_APP_LIFF_ID });

      if (liff.isInClient()) {
        isInClient.value = true;
        getProfile();
        return;
      }

      isInClient.value = false;
    });

    return {
      liffState,
      isInClient
    };
  }
});
</script>

<style lang="scss" scoped>
.hello-world {
  padding-bottom: 60px;

  &--inactive {
    font-size: 1.8rem;
    color: red;
  }

  &__title {
    font-size: 1.8rem;
    font-weight: bold;
    margin-bottom: 20px;
  }

  &__profile {
    font-size: 1.4rem;

    .profile-items {
      padding: 4px 8px;
      > img {
        display: block;
        width: 100%;
      }
    }
  }
}
</style>
