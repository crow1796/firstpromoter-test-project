<script>
import orderBy from 'lodash/orderBy'

export default {
  props: {
    data: {
      default: () => [],
    },
  },
  data() {
    return {
      sortBy: null,
      sortDir: null,
    };
  },
  computed: {
    sorted() {
      if(this.sortDir) return orderBy(this.data, 'Name', this.sortDir)

      return this.data;
    },
  },
  methods: {
    sort(field){
      if(!this.sortBy) {
        this.sortBy = field
        this.sortDir = 'asc'
        return
      }
      if(this.sortBy === field) this.sortDir = this.sortDir === 'asc' ? 'desc' : 'asc'
    }
  }
};
</script>

<template>
  <table class="table-auto dark:divide-gray-700 mx-auto w-full">
    <thead class="bg-gray-100 dark:bg-gray-700">
      <tr>
        <th
          scope="col"
          class="py-3 px-6 text-xs font-medium tracking-wider text-left text-gray-700 uppercase dark:text-gray-400 cursor-pointer"
          @click="sort('name')"
        >
          <span class="flex items-center justify-between">
            <span>Name</span>
            <span class="flex flex-col items-center text-xs">
              <span class="leading-none" v-show="!sortBy || sortDir === 'desc'">&#9650;</span>
              <span class="leading-none" v-show="!sortBy || sortDir === 'asc'">&#9660;</span>
            </span>
          </span>
        </th>
        <th
          scope="col"
          class="py-3 px-6 text-xs font-medium tracking-wider text-left text-gray-700 uppercase dark:text-gray-400 cursor-pointer"
        >Email</th>
        <th
          scope="col"
          class="py-3 px-6 text-xs font-medium tracking-wider text-left text-gray-700 uppercase dark:text-gray-400 cursor-pointer"
        >Address</th>
        <th
          scope="col"
          class="py-3 px-6 text-xs font-medium tracking-wider text-left text-gray-700 uppercase dark:text-gray-400 cursor-pointer"
        >State</th>
        <th scope="col" class="p-4">
          <span class="sr-only">Delete</span>
        </th>
      </tr>
    </thead>
    <tbody class="bg-white divide-y divide-gray-200 dark:bg-gray-800 dark:divide-gray-700">
      <tr v-if="!data.length">
        <td class="text-center py-4" colspan="5">No record(s).</td>
      </tr>
      <tr class="hover:bg-gray-100 dark:hover:bg-gray-700" v-for="user in sorted" :key="user.id">
        <td
          class="py-4 px-6 text-sm font-medium text-gray-900 whitespace-nowrap dark:text-white"
        >{{user.Name}}</td>
        <td
          class="py-4 px-6 text-sm font-medium text-gray-900 whitespace-nowrap dark:text-white"
        >{{user.email}}</td>
        <td
          class="py-4 px-6 text-sm font-medium text-gray-900 whitespace-nowrap dark:text-white"
        >{{user.street}}</td>
        <td
          class="py-4 px-6 text-sm font-medium text-gray-900 whitespace-nowrap dark:text-white capitalize"
        >
          <div class="w-full flex justify-center items-center">
            <div
              class="w-4 h-4 rounded-full"
              :class="{
              'bg-red-600': user.state === 'cancelled',
              'bg-green-400': user.state === 'active',
            }"
              :title="user.state"
            ></div>
          </div>
        </td>
        <td class="py-4 px-6 text-sm font-medium text-right whitespace-nowrap">
          <a
            href="#"
            class="text-blue-600 dark:text-blue-500 hover:underline"
            @click.prevent="$emit('delete', user)"
          >Delete</a>
        </td>
      </tr>
    </tbody>
  </table>
</template>