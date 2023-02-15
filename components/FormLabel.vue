<template>
  <div>{{ label }} <strong v-if="required" style="color: red">*</strong></div>
</template>

<script>
export default {
  props: {
    label: {
      type: String,
      default: '',
    },
    required: {
      type: Boolean,
      default: false,
    },
  },
}
</script>
