<script>
import Table from "./components/Table.vue";
import Modal from "./components/Modal.vue";

export default {
  components: {
    Table,
    Modal,
  },
  async created() {
    await this.fetchData();
  },
  data() {
    return {
      loading: true,
      data: [],
      error: null,
      filteredData: [],
      query: null,
      selectedUser: null,
    };
  },
  methods: {
    async fetchData() {
      try {
        let json = await fetch(
          "https://621eb944849220b1fc9ff8cb.mockapi.io/api/users/users",
          {
            method: "GET",
            headers: {
              "Content-Type": "application/json",
            },
          }
        ).then((res) => {
          if (!res.ok) {
            const err = new Error(res.statusText);
            err.json = res.json();
            throw err;
          }

          return res.json();
        });
        this.data = json;
        this.filteredData = this.data;
      } catch (err) {
        this.error.value = err;
        if (err.json) {
          return err.json.then((json) => {
            this.error.value.message = json.message;
          });
        }
      }

      this.loading = false;
    },
    showDeleteConfirmation(user) {
      this.selectedUser = user;
      if (this.$refs.deleteConfirmationModal)
        this.$refs.deleteConfirmationModal.show();
    },
    async confirmDelete() {
      try {
        this.loading = true;
        let json = await fetch(
          `https://621eb944849220b1fc9ff8cb.mockapi.io/api/users/users/${this.selectedUser.id}`,
          {
            method: "DELETE",
            headers: {
              "Content-Type": "application/json",
            },
          }
        ).then((res) => {
          if (!res.ok) {
            const err = new Error(res.statusText);
            err.json = res.json();
            throw err;
          }

          return res.json();
        });
        this.data = this.data.filter((u) => u.id !== this.selectedUser.id);
        this.filteredData = this.filteredData.filter((u) => u.id !== this.selectedUser.id);
        this.selectedUser = null;
      } catch (err) {
        this.error.value = err;
        if (err.json) {
          return err.json.then((json) => {
            this.error.value.message = json.message;
          });
        }
      }

      this.loading = false;
    },
    hideDeleteConfirmation() {
      if (this.$refs.deleteConfirmationModal)
        this.$refs.deleteConfirmationModal.hide();
    },
    filter() {
      if (!this.query) {
        this.filteredData = this.data;
        return;
      }

      this.filteredData = this.data.filter((u) => u.state.includes(this.query));
    },
  },
};
</script>

<template>
  <div>
    <div class="p-32" v-if="!loading">
      <div class="mb-4">
        <select
          type="text"
          class="rounded border px-4 py-2 outline-none"
          placeholder="Search name"
          v-model="query"
          @change="filter"
        >
          <option :value="null">Filter by state</option>
          <option value="active">Active</option>
          <option value="cancelled">Cancelled</option>
        </select>
      </div>
      <Table :data="filteredData" @delete="showDeleteConfirmation" />
      <Modal ref="deleteConfirmationModal">
        <div class="text-center">Are you sure you want to delete this?</div>
        <div class="mt-2 flex justify-between">
          <button
            class="bg-gray-200 h-full px-4 py-2 rounded flex-1 mr-1"
            @click="hideDeleteConfirmation"
          >No</button>
          <button
            class="bg-blue-400 h-full px-4 py-2 rounded flex-1 ml-1"
            @click="confirmDelete"
          >Yes</button>
        </div>
      </Modal>
    </div>
    <div
      class="fixed w-full h-full bg-white flex justify-center items-center t-0"
      v-if="loading"
    >Loading...</div>
  </div>
</template>
