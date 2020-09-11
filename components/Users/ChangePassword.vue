<template>
  <v-card>
    <v-card-title class="blue-grey--text">
      <v-icon left v-text="'mdi-file-move'"/>
      Change Password
    </v-card-title>
    <v-card-text class="">
      <div class="ma-4">
        <v-text-field
          v-model="formValues.oldPassword"
          type="Password"
          label="Old Password"
          id="oldPassword"/>
        <v-text-field
          v-model="formValues.newPassword"
          type="Password"
          label="New Password"
          id="newPassword"/>
        <v-text-field v-model="formValues.repeatPassword" type="Password" label="Confirm Password" id="repeatPassword"/>
      </div>
    </v-card-text>
    <v-card-actions>
      <v-spacer/>
      <v-btn
        text
        color="grey"
        class="text-capitalize"
        @click="$emit('close')"
      >
        Cancel
      </v-btn>
      <v-btn
        depressed
        color="blue-grey darken-2"
        class="white--text text-capitalize"
        @click="savePassword"
        id="savePassword"
      >
        Save
      </v-btn>
    </v-card-actions>
  </v-card>
</template>
<script>
export default {
  data() {
    return {
      formValues: {
        newPassword: '',
        repeatPassword: '',
        oldPassword: ''
      }
    }
  },
  methods: {
    savePassword() {
      if (this.formValues.newPassword === this.formValues.repeatPassword) {
        this.formValues.email = this.$auth.user.email
        this.$axios.$put(`/api/v1/users/${this.$auth.user._id}`, this.formValues)
          .then(() => {
            this.setNotify({
              message: `Successfully updated`,
              color: "green"
            })
            this.$emit("close")
            this.$auth.logout()
          })
          .catch((err) => {
            this.setNotify({
              message: `Old Password Does not Match`,
              color: "red"
            })
          })
      } else {
        this.setNotify({
          message: `Confirm Password Does not Match.`,
          color: "red"
        })
      }

    }
  }
}

</script>
