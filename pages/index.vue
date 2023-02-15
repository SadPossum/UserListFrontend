<template>
  <v-row justify="center" align="center" class="pt-8">
    <v-col cols="12">
      <v-card>
        <v-card-title> {{ 'User List' }} </v-card-title>
        <v-divider />
        <v-card-text>
          <UserList visible />
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import UserList from '~/components/UserList.vue'

export default {
  name: 'IndexPage',
  comments: { UserList },
}
</script>
