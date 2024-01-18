<!-- eslint-disable vue/multi-word-component-names -->
<template lang="">
  <div class="pagination">
    <button
      v-for="page in totalPages"
      :key="page"
      @click="handleNewPage(page)"
      class="pagination__button"
      :class="{ 'active-page': page === currentPage }"
    >
      {{ page }}
    </button>
  </div>
</template>
<script>
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'Pagination',
  props: {
    totalPages: {
      type: Number,
      required: true,
    },
    currentPage: {
      type: Number,
      required: true,
    },
  },
  methods: {
    handleNewPage(page) {
      this.$emit('handleNewPage', page);
    },
  },
};
</script>
