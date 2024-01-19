<template>
  <ul class="user-list">
    <li
      
      class="user-list__item"
      v-for="user in users"
      :key="user.id"
    >
      <div @click="handleShowUser(user.id)" class="user-card">
        <img
          :src="user.avatar"
          :alt="user.first_name"
          class="user-card__image"
        />
        <div class="user-card__info">
          <p class="user-card__name">{{ user.first_name }}</p>
          <p class="user-card__email">{{ user.email }}</p>
        </div>
      </div>
      <button class="user-list__delete" @click.prevent="deleteUser(user.id)">X</button>
    </li>
  </ul>

  <Modal
    v-if="isOpenModal"
    :bottomButtons="false"
    :setIsOpenModal="setIsOpenModal"
    title="User info"
    cancelBtnTitle="Cancel"
    successBtnTitle="Ok"
    :onClickSuccess="handleSuccessClick"
  >
    <CurrentUser
      :userData="currentUser"
      @update-user-data="
        (data) => {
          hendleTest(data);
        }
      "
    />
  </Modal>
</template>

<script>
import CurrentUser from './CurrentUser.vue';
import Modal from '../atoms/Modal.vue';

export default {
  name: 'UserList',
  emits: ['currentUser'],
  components: {
    CurrentUser,
    Modal,
  },
  props: {
    users: Array,
    onDeleteUser: Function,
  },
  data() {
    return {
      query: '',
      isOpenModal: false,
      currentUser: null,
      currentPage: 1,
      totalPages: 1,
    };
  },

  methods: {
    setIsOpenModal(state) {
      this.isOpenModal = state;
    },
    setCurrentUser(user) {
      this.currentUser = user;
    },
    handleSuccessClick(event) {
      event.preventDefault();

      console.log('handleSuccessClick...');
      this.setIsOpenModal(false);
    },

    handleShowUser(userId) {
      this.setIsOpenModal(true);
      const user = this.users.find((user) => user.id == userId);
      this.setCurrentUser(user);
      console.log(this.currentUser);
    },

    deleteUser(userId) {
      this.onDeleteUser(userId);
    },
    hendleTest(data) {
      this.$emit('currentUser', data);
      this.currentUser = { ...this.currentUser, ...data };
    },
  },
};
</script>
