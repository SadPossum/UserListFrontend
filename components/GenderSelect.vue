<template>
  <v-select
    v-if="visible"
    v-model="selectedGender"
    :items="genders"
    :rules="rules"
    label="Gender"
    clearable
  >
    <template #label>
      <FormLabel :required="required" label="Gender" />
    </template>
  </v-select>
</template>

<script>
import FormLabel from '~/components/FormLabel.vue'

export default {
  comments: { FormLabel },
  props: {
    visible: {
      type: Boolean,
      default: false,
    },
    value: {
      type: Number,
      default: null,
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
      genders: [
        { value: 1, text: 'Male' },
        { value: 2, text: 'Female' },
        { value: 3, text: 'Other' },
      ],
      selectedGender: null,
    }
  },
  watch: {
    visible(newValue) {
      if (newValue === true) {
        this.selectedGender = this.value
      }
    },
    value(newValue) {
      this.selectedGender = newValue
    },
    selectedGender(newValue) {
      this.$emit('input', newValue)
    },
  },
}
</script>
