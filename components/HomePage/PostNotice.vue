<template>
  <v-card>
    <v-card-title @click="postMode = !postMode">
      <v-avatar size="40">
        <v-icon v-text="'mdi-account'" />
      </v-avatar>
      <div>Share your ideas..</div>
      <v-spacer />
      <v-icon v-text="'mdi-lock'" />
    </v-card-title>
    <v-card-text v-if="postMode">
      <v-textarea
        v-model="formValues.description"
        :label="'Share your ideas...'"
      />
    </v-card-text>
    <v-card-actions v-if="postMode">
      <v-btn color="blue-grey" class="text-capitalize" text>
        File Upload
      </v-btn>
      <v-spacer />
      <v-btn text class="text-capitalize" @click="postMode = false">
        Cancel
      </v-btn>
      <v-btn
        depressed
        color="indigo"
        class="white--text text-capitalize"
        @click="postNotice"
      >
        Post
      </v-btn>
    </v-card-actions>
  </v-card>
</template>
<script>
export default {
  data() {
    return {
      fileDetail: [],
      file: "",
      postMode: false,
      formValues: {}
    }
  },
  methods: {
    postNotice() {
      const dataToPost = {
        notice: this.formValues.description,
        images: this.formValues.images,
        createdBy: this.$auth.user._id
      }
      this.$axios.$post("api/v1/notice", dataToPost).then(() => {
        this.setNotify({
          message: "Successfully posted notice.",
          color: "green"
        })
        this.$emit("close")
        this.postMode = false
      })
    }
  }
}
</script>
