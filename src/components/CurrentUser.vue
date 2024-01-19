<template>
  <div v-if="!isEdit" class="current-user">
    <img class="current-user__img" :src="userData.avatar" />
    <p class="current-user__info">
      Fullname: {{ userData.first_name }} {{ userData.last_name }}
    </p>
    <p class="current-user__info">Email: {{ userData.email }}</p>
    <p v-if="userData.number" class="current-user__info">
      Nubmer: {{ userData.number }}
    </p>
    <p v-if="userData.address" class="current-user__info">
      Address: {{ userData.address }}
    </p>
    <p class="current-user__info">id: {{ userData.id }}</p>

    <button class="current-user__edit" @click.prevent="handleEdit">edit</button>
  </div>

  <form
    v-if="isEdit"
    class="current-user__edit-form"
    @submit.prevent="saveChanges"
  >
    <template  v-for="(input, index) in formInputs" :key="index">
      <label class="current-user__edit-label" :for="input.id">{{ input.label }}:</label>
      <input
      class="current-user__edit-input"
        :type="input.type"
        :id="input.id"
        :required="input.required"
        v-model="editedUserData[input.model]"
      />
    </template>

    <button class="current-user__edit-submit" type="submit">Save Changes</button>
  </form>
</template>

<script>
import axios from 'axios';
export default {
  name: 'CurrentUser',
  emits: ['updateUserData'],
  props: {
    userData: {
      type: Object,
      default: () => ({}),
    },
  },
  data() {
    return {
      isEdit: false,
      editedUserData: { ...this.userData },


       formInputs: [
        { required: true, id: 'editFullName', label: 'Name', type: 'text', model: 'first_name' },
        { required: true, id: 'editEmail', label: 'Email', type: 'email', model: 'email' },
        { required: false, id: 'editTel', label: 'Phone number', type: 'tel', model: 'number' },
        { required: false,id: 'editAddres', label: 'Address', type: 'text', model: 'address' },
      ],
    };
  },
  methods: {
    handleEdit() {
      this.isEdit = !this.isEdit;
    },
    async saveChanges() {
      try {
        if (!this.userData || !this.userData.id) {
          console.error('Invalid userData:', this.userData);
          return;
        }
        const response = await axios.patch(
          `https://reqres.in/api/users/${this.userData.id}`,
          {
            ...this.editedUserData,
            number: this.editedUserData.number,
            address: this.editedUserData.address,
          }
        );

        this.$emit('updateUserData', response.data);
        this.isEdit = false;
      } catch (error) {
        console.error('Error updating user data:', error);
      }
    },
  },
};
</script>
