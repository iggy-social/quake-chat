<template>
  <BirdieFeed
    v-if="siteStore.getChatTheme == 'birdie'"
    :byDid="byDid"
    :hideCommentBox="hideCommentBox"
    :id="id"
    :master="master"
    :showQuotedPost="showQuotedPost"
  />

  <ChatFeed
    v-if="siteStore.getChatTheme == 'chat'"
    :byDid="byDid"
    :hideCommentBox="hideCommentBox"
    :id="id"
    :master="master"
    :showQuotedPost="showQuotedPost"
  />
</template>

<script>
import BirdieFeed from "./themes/birdie/BirdieFeed.vue";
import ChatFeed from "./themes/chat/ChatFeed.vue";
import { useSiteStore } from '~/store/site';

export default {
  name: "IggyFeed",

  props: [
    "byDid", // if looking for posts by a specific user (user's DID)
    "hideCommentBox", // if true, we'll hide the comment box
    "id", // id (optional) is the post id that this component looks for replies to
    "master", // if there's a master post, we'll show it at the top
    "showQuotedPost" // if true, we'll show the quoted posts (for any post that has a quote)
  ],

  components: {
    BirdieFeed,
    ChatFeed
  },

  setup() {
    const siteStore = useSiteStore();

    return {
      siteStore
    }
  },
}
</script>
