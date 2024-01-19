<template>
  <header class="header">
    <div class="filter">
      <input
        class="filter__input"
        placeholder="Search by name"
        type="text"
        :value="query"
        @input="handleInputChange"
      />
    </div>

    <button
      :class="{ 'close-user-btn': isShowNewUser }"
      class="show-user-btn"
      @click.prevent="handleShowUser"
    >
      {{ buttonText }}
    </button>
  </header>

  <div v-if="isShowNewUser" class="new-user">
    <h3 class="new-user__title">Add New User</h3>
    <form class="new-user__form" @submit.prevent="addUser">
      <label for="name" class="new-user__label">Name:</label>
      <input
        type="text"
        id="name"
        class="new-user__input"
        v-model="newUser.first_name"
        required
      />

      <label for="email" class="new-user__label">Email:</label>
      <input
        type="email"
        id="email"
        class="new-user__input"
        v-model="newUser.email"
        required
      />

      <button type="submit" class="new-user__submit">Add User</button>
    </form>
  </div>

  <h1 v-if="isLoading"><Loader /></h1>
  <UserList
    v-if="!isLoading"
    :users="paginatedUsers"
    :onDeleteUser="handleDeleteUser"
    @currentUser="
      (data) => {
        handleCurrentUserEvent(data);
      }
    "
  />
  <div class="notification is-warning" v-if="paginatedUsers.length === 0">
    There are no users matching current filter criteria
  </div>
  <div v-if="paginatedUsers.length > 0" class="pagination">
    <button
      class="pagination__button"
      @click="handlePageChange('prev')"
      :disabled="currentPage === 1"
    >
      prev
    </button>
    <span>{{ currentPage }}/{{ totalPages }}</span>
    <button
      class="pagination__button"
      @click="handlePageChange('next')"
      :disabled="
        currentPage === totalPages ||
        currentPage * per_page >= filteredUsers.length
      "
    >
      next
    </button>
  </div>
</template>

<script>
import UserList from './components/UserList.vue';
import Loader from './atoms/Loader.vue';
import axios from 'axios';

export default {
  name: 'App',
  components: {
    UserList,
    Loader,
  },
  data() {
    return {
      users: [],
      query: '',
      isOpenModal: false,
      currentUser: null,
      currentPage: 1,
      totalPages: 1,
      isLoading: true,
      per_page: 6,
      errorMessage: '',
      isShowNewUser: false,

      newUser: {
        first_name: '',
        email: '',
      },
    };
  },

  computed: {
    filteredUsers() {
      const lowerCaseQuery = this.query.toLowerCase();
      return this.users.filter((user) => {
        return user.first_name.toLowerCase().includes(lowerCaseQuery);
      });
    },
    paginatedUsers() {
      const startIndex = (this.currentPage - 1) * this.per_page;
      const endIndex = startIndex + this.per_page;
      return this.filteredUsers.slice(startIndex, endIndex);
    },

    buttonText() {
      return this.isShowNewUser ? 'Close Add New User' : 'Add new User';
    },
  },

  mounted() {
    this.fetchUsers();
  },
  methods: {
    handleShowUser() {
      this.isShowNewUser = !this.isShowNewUser;
    },

    handleCurrentUserEvent(data) {
      this.users = this.users.map((user) => {
        if (user.id == data.id) {
          const updatedUser = { ...user, ...data };
          return updatedUser;
        } else {
          return user;
        }
      });
    },
    async fetchUsers() {
      try {
        let page = 1;
        do {
          const getUsers = await axios.get(
            `https://reqres.in/api/users?page=${page}`
          );

          getUsers.data.data.forEach((user) => {
            user.number = null;
            user.address = null;
          });

          this.users = this.users.concat(getUsers.data.data);
          this.totalPages = getUsers.data.total_pages;
          this.per_page = getUsers.data.per_page;
          page++;
        } while (page <= this.totalPages);
      } catch (error) {
        console.error('Error:', error);
      } finally {
        this.isLoading = false;
      }
    },

    async addUser() {
      try {
        const newUser = {
          first_name: this.newUser.first_name,
          email: this.newUser.email,
          avatar:
            'https://www.kindpng.com/picc/m/488-4888087_silhouette-user-person-user-silhouette-hd-png-download.png',
        };

        const res = await axios.post(`https://reqres.in/api/users`, newUser);
        const addedUser = res.data;

        this.users.unshift(addedUser);
        this.totalPages = Math.ceil(this.filteredUsers.length / this.per_page);

        this.newUser.first_name = '';
        this.newUser.email = '';
      } catch (error) {
        console.error(`Error added}:`, error);
        this.errorMessage = 'Error added';
      }
    },

    async handleDeleteUser(userId) {
      try {
        await axios.delete(`https://reqres.in/api/users/${userId}`);
        console.log(`User with ID ${userId} deleted successfully.`);

        this.users = this.users.filter((user) => user.id !== userId);
      } catch (error) {
        this.errorMessage = 'Error deleted';
        console.error(`Error deleting user with ID ${userId}:`, error);
      }
    },

    handleNewPage(page) {
      this.currentPage = page;
    },

    handleInputChange(event) {
      this.query = event.target.value;
      this.currentPage = 1;
    },
    handlePageChange(direction) {
      if (direction === 'prev' && this.currentPage > 1) {
        this.currentPage--;
      } else if (direction === 'next' && this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
  },
};
</script>
