<template>
  <button class="btn btn-outline-primary me-2 mt-2 "
   @click="toggleEmojiPicker">
   <i class="bi bi-emoji-smile"></i>
   </button>

    <div class="modal fade" id="emojiModal" tabindex="-1" aria-labelledby="emojiModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="emojiModalLabel">Pick an Emoji</h1>
            <button id="closeEmojiModal" type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <Picker
              v-if="emojiPickerSelected"
              :data="emojiIndex"
              set="apple"
              title="Pick your emoji…"
              emoji="point_up"
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
import { Picker, EmojiIndex } from 'emoji-mart-vue-fast/src'
import data from 'emoji-mart-vue-fast/data/apple.json'

export default {
  name: 'EmojiPickerWithModal',
  components: {
    Picker,
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
      } else if (modalInstance !== null) {
        modalInstance.hide();
      }
    }

    const handleEmojiSelect = (emoji) => {
      context.emit('updateEmoji', emoji.native);
      emojiPickerSelected.value = false;
      if (modalInstance !== null) {
        modalInstance.hide();
      }
    }

    return {
      emojiPickerSelected,
      emojiIndex,
      toggleEmojiPicker,
      handleEmojiSelect,
    }
  },
}
</script>
