<template>
  <div class="filter">
    <input
      class="filter__input"
      placeholder="Search by name"
      type="text"
      :value="query"
      @input="handleInputChange"
    />
  </div>
</template>
<script>
export default {
  name: 'App',
  props: {
    query: String,
  },
  methods: {
    handleInputChange(event) {
      this.$emit('input', event.target.value);
    },
  },
};
</script>
