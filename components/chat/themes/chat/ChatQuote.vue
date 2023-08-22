<template>
<div v-if="post">
  <small class="fst-italic">
    <i class="bi bi-reply" /> Reply to 
    <NuxtLink v-if="authorDomain" class="link-without-color hover-color" :to="'/profile/?id='+authorDomain">{{showDomainOrAddressOrAnon}}</NuxtLink>
    <span v-if="!authorDomain">{{showDomainOrAddressOrAnon}}</span>
  </small>
</div>
</template>

<script>
import { ethers } from 'ethers';
import sanitizeHtml from 'sanitize-html';
import { useEthers, shortenAddress } from 'vue-dapp';
import ResolverAbi from "~/assets/abi/ResolverAbi.json";
import resolvers from "~/assets/data/resolvers.json";
import { useUserStore } from '~/store/user';
import ProfileImage from "~/components/profile/ProfileImage.vue";
import { imgParsing, imgWithoutExtensionParsing, urlParsing, youtubeParsing } from '~/utils/textUtils';

export default {
  name: "ChatQuote",
  props: ["post"], // quote post: body, stream_id, pfp, username, address

  components: {
    ProfileImage
  },

  data() {
    return {
      authorAddress: null,
      authorDomain: null,
      parsedText: null,
      postLengthLimit: 300,
      showFullText: false
    }
  },

  created() {
    if (this.post.address) {
      this.fetchAuthorDomain();
    }

    this.parsePostText();
  },

  computed: {
    getOrbisImage() {
      if (this.post.pfp) {
        return this.post.pfp;
      }

      return null;
    },

    showDomainOrAddressOrAnon() {
      if (this.authorDomain) {
        return this.authorDomain;
      } else if (this.post.address) {
        return this.shortenAddress(this.post.address);
      } else {
        return "Anon";
      }
    },

    showDomainOrFullAddress() {
      if (this.authorDomain) {
        return this.authorDomain;
      } else if (this.post.address) {
        return this.post.address;
      }

      return null;
    },
  },

  methods: {
    async fetchAuthorDomain() {
      // find out if post author has a domain name
      this.authorAddress = this.post.address;

      if (this.authorAddress) {
        // check session storage if author's domain is already stored
        const storedDomain = sessionStorage.getItem(String(this.authorAddress).toLowerCase());

        if (storedDomain) {
          this.authorDomain = storedDomain;
        } else {
          // fetch provider from hardcoded RPCs
          let provider = this.$getFallbackProvider(this.$config.supportedChainId);

          if (this.isActivated && this.chainId === this.$config.supportedChainId) {
            // fetch provider from user's MetaMask
            provider = this.signer;
          }

          const contract = new ethers.Contract(resolvers[this.$config.supportedChainId], ResolverAbi, provider);

          // get author's default domain
          const domainName = await contract.getDefaultDomain(
            String(this.authorAddress).toLowerCase(), 
            String(this.$config.tldName).toLowerCase()
          );

          if (domainName) {
            this.authorDomain = domainName + this.$config.tldName;
            sessionStorage.setItem(String(this.authorAddress).toLowerCase(), this.authorDomain);
          } 
        }
      }
    },

    openPostDetails() {
      this.$router.push({ name: 'post', query: { id: this.post.stream_id } });
    },

    parsePostText() {
      let postText = this.post.body;

      postText = sanitizeHtml(postText, {
        allowedTags: [ 'li', 'ul', 'ol', 'br', 'em', 'strong', 'i', 'b' ],
        allowedAttributes: {}
      });

      postText = imgParsing(postText);
      postText = imgWithoutExtensionParsing(postText);
      postText = youtubeParsing(postText);
      postText = urlParsing(postText);

      this.parsedText = postText.replace(/(\r\n|\n|\r)/gm, "<br/>");
    }
  },

  setup() {
    const route = useRoute();
    const { address, chainId, isActivated, signer } = useEthers();
    const userStore = useUserStore();

    return { address, chainId, isActivated, route, shortenAddress, signer, userStore }
  },
}
</script>