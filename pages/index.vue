<template>
  <IggyFeed class="mt-1" :showQuotedPost="$config.showRepliesOnHomepage" />
</template>

<script>
import { useEthers } from 'vue-dapp'
import IggyFeed from "../components/chat/IggyFeed.vue"

export default {
  name: "index",

  components: {
    IggyFeed
  },

  setup() {
    const { address, chainId } = useEthers()

    return { address, chainId }
  }
}
</script>