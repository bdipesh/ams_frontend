<template>
  <v-card>
    <v-img src="/loginBackground.png" aspect-ratio="2">
      <v-row class="justify-center mt-3">
        <v-col md="5" sm="12">
          <v-card class="blue lighten-5">
            <v-card-title class="text--center">
              <v-col class="text-center">
                <v-img src="/logo.png" height="50" class="ma-auto" width="50" />
                <div>
                  Attendance Management System
                </div>
              </v-col>
            </v-card-title>
            <v-card flat class="mx-4">
              <v-card-text class="pb-0">
                <v-text-field v-model="email" label="Username" />
                <v-text-field
                  v-model="password"
                  label="Password"
                  type="Password"
                />
              </v-card-text>
              <v-card-actions class="text-center">
                <v-btn
                  color="indigo"
                  depressed
                  class="white--text ml-2 text-capitalize"
                  @click="login"
                >
                  Login
                </v-btn>
                <v-spacer />
                <v-checkbox label="Remember me" class="mt-3" />
              </v-card-actions>
            </v-card>

            <v-card-actions>
              <v-btn
                text
                color=""
                class="indigo--text text-capitalize"
                v-text="'Forget Password ?'"
              />
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </v-img>
  </v-card>
</template>
<script>
    export default {
      layout: "blank",
      data() {
        return {
          email: "",
          password: ""
        }
      },
      created() {
        document.title = "Login | Attendance management System"
      },
      methods: {
        async login() {
          let data = { email: this.email, password: this.password }
          await this.$auth
            .loginWith("local", { data })
            .then(() => {
              this.setNotify({
                message: "Successfully Login. Welcome to AMS!",
                color: "green"
              })
              this.$router.push("/")
            })
            .catch(() => {
              this.setNotify({
                message: "Invalid Username or Password.",
                color: "red"
              })
            })
        }
      }
    }
</script>
