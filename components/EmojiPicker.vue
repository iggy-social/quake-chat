<template>
  <button class="btn btn-outline-primary me-2 mt-2 "
   @click="toggle">
   <i class="bi bi-emoji-smile"></i>
   EMO</button>
   <Picker
    v-if="emojiPickerSelected"
    :data="emojiIndex"
    set="apple" 
    title="Pick your emoji…"
    emoji="point_up"
    @select="convertEmoji"
    class="emoji-mart-category"
    :style="{ backgroundColor: '#222529', color: '#FFFFFF',  }" 
  />
</template>

<script>
import { ref } from 'vue'
import { Picker, EmojiIndex } from 'emoji-mart-vue-fast/src'
import data from 'emoji-mart-vue-fast/data/apple.json'

export default {
  name: 'EmojiPicker',
  components: {
    Picker,
  },
  emits: ['updateEmoji'],
  setup(props, context) {
    const emojiPickerSelected = ref(false)
    let emojiIndex = new EmojiIndex(data)

    const toggle = () => {
      emojiPickerSelected.value = !emojiPickerSelected.value
    }

    const convertEmoji = (emoji) => {
      context.emit('updateEmoji', emoji.native)
    }

    return {
      emojiPickerSelected,
      emojiIndex,
      toggle,
      convertEmoji,
    }
  },
}
</script>
