<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="modal_background" @click="closeModal">
    <div class="modal_container" @click.stop>
      <div class="modal_button_close__wraper">
        <button class="modal_button_close" @click="closeModal">X</button>
      </div>

      <h1 class="modal_title">{{ title }}</h1>

      <form class="modal_form">
        <slot></slot>
        <div v-if="bottomButtons" class="modal_buttons_wrapper">
          <button class="modal_button_cancel" @click="closeModal">
            {{ cancelBtnTitle }}
          </button>
          <button class="modal_button_success" @click="onClickSuccess">
            {{ successBtnTitle }}
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'Modal',
  props: {
    setIsOpenModal: {
      type: Function,
      required: true,
    },
    title: {
      type: String,
      required: true,
    },
    successBtnTitle: {
      type: String,
      required: true,
    },
    cancelBtnTitle: {
      type: String,
      required: true,
    },
    onClickSuccess: {
      type: Function,
      required: true,
    },
    bottomButtons: {
      type: Boolean,
      default: true,
    },
  },
  methods: {
    closeModal() {
      this.setIsOpenModal(false);
    },
  },
};
</script>
