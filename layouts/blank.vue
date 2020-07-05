<template>
  <v-app>
    <v-container>
      <v-row align="center" justify="center">
        <v-col>
          <nuxt />
        </v-col>
      </v-row>
      <vue-snackbar />
    </v-container>
  </v-app>
</template>
<script>
import VueSnackbar from "../components/LayoutUtils/VueSnackbar"
export default {
  components: { VueSnackbar }
}
</script>
