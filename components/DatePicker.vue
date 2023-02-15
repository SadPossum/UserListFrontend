<template>
  <v-menu
    ref="menu"
    v-model="menuOpen"
    :close-on-content-click="false"
    transition="scale-transition"
    offset-y
    min-width="0"
  >
    <template #activator="{ on }">
      <v-text-field
        v-model="formattedDate"
        :label="label"
        :rules="[...dateRules]"
        prepend-icon="mdi-calendar"
        clearable
        readonly
        v-on="on"
        @click:clear="clearDate"
      >
        <template #label>
          <FormLabel :label="label" :required="required" />
        </template>
      </v-text-field>
    </template>
    <v-date-picker
      v-model="date"
      no-title
      @input="updateDate"
      @change="closeDatePicker"
    ></v-date-picker>
  </v-menu>
</template>

<script>
import FormLabel from '~/components/FormLabel.vue'

export default {
  comments: { FormLabel },
  props: {
    value: {
      type: String,
      default: null,
    },
    label: {
      type: String,
      default: 'Date',
    },
    required: {
      type: Boolean,
      default: false,
    },
    rules: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      menuOpen: false,
      date: null,
      formattedDate: null,
      dateRules: [true],
    }
  },
  watch: {
    rules() {
      if (this.rules && this.rules.length > 0) {
        this.dateRules = this.rules.map((rule) => rule(this.date))
      } else {
        this.dateRules = [true]
      }
    },
    value() {
      if (this.value) {
        this.updateDate(this.value)
      } else {
        this.updateDate(null)
      }
    },
  },
  methods: {
    updateDate(date) {
      this.date = date
      this.$emit('input', date)
      this.updateFormattedDate(this.formatDate(date))
    },
    updateFormattedDate(formattedDate) {
      if (formattedDate) {
        this.formattedDate = formattedDate
      } else {
        this.formattedDate = null
      }
    },
    closeDatePicker() {
      this.menuOpen = false
    },
    formatDate(date) {
      if (date) {
        return new Intl.DateTimeFormat('ru-RU').format(new Date(date))
      } else {
        return null
      }
    },
    clearDate() {
      this.updateDate(null)
    },
  },
}
</script>
