<template>
  <button data-bs-target="#emojiModal" class="btn btn-outline-primary me-2 mt-2"
   @click="toggleEmojiPicker" >
    <i class="bi bi-emoji-smile"></i>
  </button>

  <div class="modal fade" id="emojiModal" tabindex="-1" aria-labelledby="emojiModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="emojiModalLabel">Pick an Emoji</h1>
          <button id="closeEmojiModal" type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"
                  @click="closeEmojiPicker"></button>
        </div>
        <div class="modal-body">
          <EmojiMartPicker
            v-if="emojiPickerSelected"
            :data="emojiIndex"
            set="apple"
            @select="handleEmojiSelect"
            class="emoji-mart-category"
            :style="{ backgroundColor: '#222529', color: '#FFFFFF' }"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'
import { EmojiIndex } from 'emoji-mart-vue-fast/src/utils/emoji-data'
import EmojiMartPicker from 'emoji-mart-vue-fast/src/components/Picker'

import data from 'emoji-mart-vue-fast/data/all.json'

export default {
  name: 'EmojiPickerWithModal',
  components: {
    EmojiMartPicker,
  },
  emits: ['updateEmoji'],

  setup(props, context) {
    const emojiPickerSelected = ref(false)
    const emojiIndex = new EmojiIndex(data)
    let modalInstance = null;

    const toggleEmojiPicker = () => {
      emojiPickerSelected.value = !emojiPickerSelected.value;

      if (emojiPickerSelected.value) {
        modalInstance = new bootstrap.Modal(document.getElementById('emojiModal'));
        modalInstance.show();
      } else if (modalInstance !== null && modalInstance._isShown) {
        modalInstance.hide();
      }
    }

    const closeEmojiPicker = () => {
      emojiPickerSelected.value = false;

      if (modalInstance !== null && modalInstance._isShown) {
        modalInstance.hide();
      }
    }

    const handleEmojiSelect = (emoji) => {
      context.emit('updateEmoji', emoji.native);
      closeEmojiPicker();
    }

    return {
      emojiPickerSelected,
      emojiIndex,
      toggleEmojiPicker,
      handleEmojiSelect,
      closeEmojiPicker,
    }
  },
}
</script>
