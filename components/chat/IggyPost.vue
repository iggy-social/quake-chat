<template>
  <BirdiePost v-if="siteStore.getChatTheme == 'birdie'" :post="post" :showQuotedPost="showQuotedPost" />
  <ChatPost v-if="siteStore.getChatTheme == 'chat'" :post="post" :showQuotedPost="showQuotedPost" />
</template>

<script>
import BirdiePost from "./themes/birdie/BirdiePost.vue";
import ChatPost from "./themes/chat/ChatPost.vue";
import { useSiteStore } from '~/store/site';

export default {
  name: "IggyPost",

  props: ["post", "showQuotedPost"],

  components: {
    BirdiePost,
    ChatPost
  },

  setup() {
    const siteStore = useSiteStore();

    return {
      siteStore
    }
  },
}
</script>
