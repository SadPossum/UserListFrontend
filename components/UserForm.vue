<template>
  <v-dialog
    :value="visible"
    :fullscreen="$vuetify.breakpoint.xs"
    width="600"
    persistent
    scrollable
    @keydown.esc="close"
  >
    <v-card>
      <v-toolbar color="primary" class="flex-grow-0 white--text">
        <v-toolbar-title>Add review</v-toolbar-title>
        <v-spacer />
        <v-toolbar-items>
          <v-btn color="white" text icon @click="close">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-toolbar-items>
      </v-toolbar>
      <v-card-text class="py-2 px-8">
        <v-form ref="form" v-model="formValid" lazy-validation>
          <v-text-field
            v-model="editedItem.fullName"
            :rules="[
              (v) => required(v, 'Full name is required.'),
              (v) =>
                (v && v.length >= 3 && v.length <= 100) ||
                'Full name must be between 3 and 100 characters.',
            ]"
            clearable
          >
            <template #label>
              <FormLabel label="Full Name" required />
            </template>
          </v-text-field>

          <DatePicker
            v-model="editedItem.dateOfBirth"
            label="Date of birth"
            required
            :rules="[
              (v) => required(v, 'Date of birth is required'),
              (v) =>
                (v && new Date(v).getTime() < new Date().getTime()) ||
                'Date of birth must be in the past.',
            ]"
          />

          <v-text-field
            v-model="editedItem.country"
            :rules="[
              (v) => required(v, 'Country is required.'),
              (v) =>
                (v.length >= 2 && v.length <= 100) ||
                'Country must be between 2 and 100 characters.',
            ]"
            clearable
          >
            <template #label>
              <FormLabel label="Country" required />
            </template>
          </v-text-field>

          <v-text-field
            v-model="editedItem.city"
            :rules="[
              (v) => required(v, 'City is required.'),
              (v) =>
                (v && v.length >= 2 && v.length <= 100) ||
                'City must be between 2 and 100 characters.',
            ]"
            label="City"
            clearable
          >
            <template #label>
              <FormLabel label="City" required />
            </template>
          </v-text-field>

          <GenderSelect
            v-model="editedItem.gender"
            :visible="visible"
            :rules="[(v) => required(v, 'Gender is required.')]"
            required
          />
        </v-form>
      </v-card-text>
      <v-card-actions class="pb-0 pt-4 px-4">
        <v-row align="center" justify="center" no-gutters>
          <v-col>
            <v-row justify="center" no-gutters>
              <v-btn
                class="mx-2 mb-4"
                :disabled="!formValid || formDisableActions"
                color="primary"
                @click="save"
              >
                Add
              </v-btn>
              <v-btn class="mx-2 mb-4" @click="close"> Close </v-btn>
            </v-row>
          </v-col>
        </v-row>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
import FormLabel from '~/components/FormLabel.vue'
import DatePicker from '~/components/DatePicker.vue'

export default {
  components: { FormLabel, DatePicker },
  props: {
    visible: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      form: null,
      menu: null,
      showMenu: null,
      formValid: true,
      formDisableActions: false,
      defaultItem: {
        fullName: '',
        dateOfBirth: null,
        country: '',
        city: '',
        gender: 0,
      },
      editedItem: {
        fullName: '',
        dateOfBirth: null,
        country: '',
        city: '',
        gender: 0,
      },
    }
  },
  watch: {
    visible(newValue) {
      if (newValue === true) {
        this.editedItem = JSON.parse(JSON.stringify(this.defaultItem))
      }
    },
  },
  methods: {
    async save() {
      if (this.$refs.form.validate()) {
        this.formDisableActions = true
        const item = {
          ...this.editedItem,
        }

        try {
          await this.$axios.$post(`api/user`, item)
        } catch (err) {
          this.formDisableActions = false
          return
        }

        this.formDisableActions = false
        this.close()
        this.$emit('saved')
      }
    },
    close() {
      this.$emit('update:visible', false)
    },
    handleError() {
      this.formDisableActions = false
    },
    required(value, text) {
      return (
        (value !== undefined && value !== null && value !== '') ||
        text ||
        'Required'
      )
    },
  },
}
</script>
