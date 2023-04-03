<template>
  <div v-if="visible">
    <v-row justify="center" align="center" no-gutters class="pb-5 pb-sm-3">
      <v-col cols="12" sm="4">
        <v-text-field
          v-model="searchFullName"
          class="pr-sm-4"
          label="Full Name"
          clearable
          @keyup.enter="searchUsers"
        >
          <template #label>
            <FormLabel label="Full Name" required />
          </template>
        </v-text-field>
      </v-col>
      <v-col cols="12" sm="4" class="pr-sm-2">
        <date-picker v-model="searchDateOfBirth" label="Date of Birth" />
      </v-col>
      <v-col cols="12" sm="4">
        <div class="d-flex">
          <span class="text-center" @click="searchUsers">
            <v-btn color="grey" icon>
              <v-icon>mdi-magnify</v-icon>
            </v-btn>
            <span class="hidden-sm-and-up">Search</span>
          </span>
          <v-spacer />
          <v-btn color="primary" @click="userFormVisible = true">
            Add User
          </v-btn>
        </div>
      </v-col>
    </v-row>
    <v-divider />
    <v-data-table
      :headers="headers"
      :items="users"
      :loading="loading"
      :options.sync="options"
      :server-items-length="totalUsers"
      :no-data-text="noDataText"
      :multi-sort="true"
    >
      <template #[`item.dateOfBirth`]="{ item }">
        {{ formatDate(item.dateOfBirth) }}
      </template>
      <template #[`item.gender`]="{ item }">
        {{ formatGender(item.gender) }}
      </template>
    </v-data-table>

    <UserForm :visible.sync="userFormVisible" @saved="fetchUsers" />
  </div>
</template>

<script>
import FormLabel from '~/components/FormLabel.vue'
import UserForm from '~/components/UserForm.vue'
import DatePicker from '~/components/DatePicker.vue'

export default {
  components: { UserForm, DatePicker, FormLabel },
  props: {
    visible: {
      type: Boolean,
      default: false,
    },
  },
  data: () => ({
    headers: [
      { text: 'ID', value: 'id' },
      { text: 'Full Name', value: 'fullName' },
      { text: 'Date of Birth', value: 'dateOfBirth' },
      { text: 'Country', value: 'country' },
      { text: 'City', value: 'city' },
      { text: 'Gender', value: 'gender' },
    ],
    users: [],
    searchFullName: '',
    searchDateOfBirth: '',
    options: {
      sortBy: ['id'],
      sortDesc: [false],
      page: 1,
      itemsPerPage: 10,
    },
    totalUsers: 0,
    loading: false,
    noDataText: 'No users found',
    userFormVisible: false,
  }),
  watch: {
    visible: {
      handler() {
        if (this.visible === true) {
          this.fetchUsers()
        }
      },
      immediate: true,
    },
    options: {
      handler() {
        this.fetchUsers()
      },
      deep: true,
    },
  },
  methods: {
    async fetchUsers() {
      this.loading = true

      let sort = null
      if (this.options.sortBy.length > 0) {
        const sortItems = this.options.sortBy.map((field, index) => ({
          propertyName: field,
          isDescending: this.options.sortDesc[index],
        }))
        sort = this.mapArrayToParamsObject(sortItems, 'sort')
      }

      let pagination = null
      if (this.options.itemsPerPage > 0) {
        pagination = {
          'pagination.pageNumber': this.options.page,
          'pagination.pageSize': this.options.itemsPerPage,
        }
      }

      const params = {
        fullName: this.searchFullName,
        dateOfBirth: this.searchDateOfBirth,
        ...pagination,
        ...sort,
      }

      let response = null
      try {
        response = await this.$axios.get('/api/user', { params })
      } catch (err) {
        this.loading = false
        return
      }

      this.users = response.data.items
      this.totalUsers = response.data.totalItemsCount

      this.loading = false
    },
    mapArrayToParamsObject(array, prefix) {
      const params = {}
      array.forEach((item, index) => {
        Object.entries(item).forEach(([key, value]) => {
          const paramKey = `${prefix}[${index}].${key}`
          params[paramKey] = value
        })
      })
      return params
    },
    formatDate(date) {
      return new Intl.DateTimeFormat('ru-RU').format(new Date(date))
    },
    formatGender(gender) {
      switch (gender) {
        case 1:
          return 'Male'
        case 2:
          return 'Female'
        case 3:
          return 'Other'
        default:
          return ''
      }
    },
    searchUsers() {
      this.options.page = 1
      this.fetchUsers()
    },
    handleError() {
      this.loading = false
    },
  },
}
</script>
