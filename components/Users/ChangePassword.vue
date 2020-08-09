<template>
  <v-dialog v-model="changePassword" width="600" persistent>
    <v-card>
      <v-card-title class="blue-grey--text">
        <v-icon left v-text="'mdi-file-move'" />
        Change Password
      </v-card-title>
      <v-card-text class="">
        <div class="ma-4">
          <v-text-field v-model="formValues.newPass" label="New Password" id="password" />
          <v-text-field  label="Confirm Password" />
        </div>
      </v-card-text>
      <v-card-actions>
        <v-spacer />
        <v-btn
          text
          color="grey"
          class="text-capitalize"
          @click="changePassword = false"
        >
          Cancel
        </v-btn>
        <v-btn
          depressed
          color="blue-grey darken-2"
          class="white--text text-capitalize"
          @click="savePassword"
          id="savepassword"
        >
          Save
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>
<script>
  export default {
    data(){
      return{
        changePassword: false
      }
    },
    methods:{
      savePassword(){
        const passchange=new FormData()
        passchange.append("password",this.formValues)
        this.$axios
          .$put(`/api/v1/users/${this.formValues._id}`, passchange)
        .then(() => {
          this.setNotify({
            message: `Successfully updated`,
            color: "green"
          })
          this.$emit("close")
        })
      }
    }
  }

</script>
