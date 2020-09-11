<template>
  <v-row v-if="user" align="center">
    <v-col cols="auto">
      <v-avatar size="50">
        <v-img
          :src="'http://localhost:9000/static/' + user.picture"
          contain
          aspect-ratio="2.0"
        />
      </v-avatar>
    </v-col>
    <v-col cols="auto" class="px-0">
      <div class="body-1 blue-grey--text font-weight-bold">
        {{ user.name }}
      </div>
      <div class="grey--text caption">
        {{ user.role }}
      </div>
    </v-col>
  </v-row>
</template>
<script>
  export default {
    props: ["user"]
  }
</script>
