<template>
  <v-card>
    <v-card-title>
      {{ update ? 'Update Details' : 'Add New User' }}
    </v-card-title>
    <v-divider class="my-0" />
    <v-card-text>
      <v-form ref="form" v-model="valid" :lazy-validation="lazy">
        <v-row>
          <v-col cols="6">
            <v-text-field
              v-model="formValues.name"
              :rules="requiredRules"
              label="Name *"
            />
          </v-col>
          <v-col cols="6">
            <v-text-field
              v-model="formValues.email"
              :rules="emailRules"
              type="email"
              label="Email *"
            />
          </v-col>
          <v-col cols="6">
            <v-text-field
              v-model="formValues.phone"
              :rules="phoneRules"
              label="Phone *"
            />
          </v-col>
          <v-col v-if="!update" cols="6">
            <div>Gender</div>
            <v-row>
              <v-col
                v-for="(gender, index) in genderChoices"
                :key="index"
                cols="auto"
                @click="formValues.gender = gender"
              >
                <v-btn
                  :outlined="formValues.gender !== gender"
                  rounded
                  depressed
                  color="blue-grey darken-2"
                  class="white--text"

                >
                  {{ gender }}
                </v-btn>
              </v-col>
            </v-row>
          </v-col>
          <v-col cols="6">
            <v-menu
              v-model="datePicker"
              :close-on-content-click="false"
              :nudge-right="40"
              transition="scale-transition"
              offset-y
              min-width="290px"
            >
              <template v-slot:activator="{ on }">
                <v-text-field
                  v-model="formValues.dob"
                  label="Date of birth *"
                  :rules="requiredRules"
                  prepend-icon="mdi-calendar"
                  readonly
                  v-on="on"
                />
              </template>
              <v-date-picker
                v-model="formValues.dob"
                @input="datePicker = false"
              />
            </v-menu>
          </v-col>
          <v-col v-if="!update" cols="6">
            <v-select
              v-model="formValues.course"
              :items="userCourseChoices"
              :rules="requiredRules"
              item-text="courseName"
              item-value="_id"
              label="Course *"
              multiple
              @click="getCourse()"
            />
          </v-col>
          <v-col v-if="(as === 'teacher' && !update) || !$route.query.selected_batch" cols="6">
            <v-select
              v-model="formValues.batch"
              :items="userBatchChoices"
              item-text="batchName"
              :rules="requiredRules"
              item-value="_id"
              label="Batch *"
              :multiple="as === 'teacher'"
              @click="getBatch()"
            />
          </v-col>
          <v-col cols="6">
            <v-file-input v-model="formValues.image" label="Profile Picture" />
          </v-col>
        </v-row>
      </v-form>
    </v-card-text>
    <v-divider class="my-0" />
    <v-card-actions>
      <v-spacer />
      <v-btn
        color="grey"
        text
        class="text-capitalize"
        @click="$refs.form.resetValidation(), $emit('close')"
      >
        Cancel
      </v-btn>
      <v-btn
        depressed
        color="blue-grey"
        class="white--text text-capitalize"
        @click="createUser"
      >
        Save
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
  export default {
    name: "AddNewUser",
    components: {},
    props: {
      "title": {
        type: String,
        default: ''
      },
      actionData: {
        type: Object,
        default () {
          return null
        }
      },
      as: {
        type: String,
        default: ''
      },
      selectedBatch: {
        type: String,
        default: ''
      },
      update: {
        type: Boolean,
        default: false
      }
    },
    data() {
      return {
        genderChoices: ["Male", "Female", "Others"],
        userRoleChoices: ["Student", "Teacher"],
        // reactive component
        datePicker: false,
        userCourseChoices: [],
        userBatchChoices: [],
        formValues: {
          gender: "Male"
        },
        valid: true,
        requiredRules: [v => !!v || "This field is required"],
        emailRules: [
          v => !!v || "Email field is required",
          v => /.+@.+\..+/.test(v) || "E-mail must be valid"
        ],
        phoneRules: [
          v => !!v || "This field is required"
        ]
      }
    },
    created() {
      this.getBatch()
      this.getCourse()
      if (this.actionData) {
        this.formValues = {...this.actionData, ...{
            gender: "Male"
          }}
          this.formValues.course = this.actionData.course ? this.actionData.course.split(',') : []
          this.formValues.batch = this.actionData.batch
      }


    },
    methods: {
      createUserRules () {
        if(this.as && this.as === 'teacher') {
          this.formValues.role = 'Teacher'
        } else {
          this.formValues.role = 'Student'
          if(this.$route.query.selected_batch) {
            this.formValues.batch = this.$route.query.selected_batch || this.selectedBatch
          }
        }
      },
      createUser() {
        this.createUserRules()
        const dataToPost = new FormData()
        dataToPost.append("name", this.formValues.name)
        dataToPost.append("email", this.formValues.email)
        dataToPost.append("phone", this.formValues.phone)
        dataToPost.append("role", this.formValues.role)
        dataToPost.append("gender", this.formValues.gender)
        dataToPost.append("password", "hellonepal")
        dataToPost.append("picture", this.formValues.picture)
        dataToPost.append("batch", this.formValues.batch)
        dataToPost.append("course", this.formValues.course)
        dataToPost.append("dob", this.formValues.dob)
        if (this.$refs.form.validate()) {
          if (this.formValues._id) {
            this.$axios
              .$patch(`/api/v1/users/${this.formValues._id}`, dataToPost)
              .then(() => {
                this.setNotify({
                  message: `Successfully updated ${this.formValues.name} Details.`,
                  color: "green"
                })
                this.$refs.form.resetValidation()
                this.$emit("close")
              })
              .catch(() => {
                this.setNotify({
                  message: `Something went wrong.`,
                  color: "red"
                })
              })
          } else {
            this.$axios
              .$post("/api/v1/users", dataToPost)
              .then(() => {
                this.setNotify({
                  message: `Successfully created ${this.formValues.role}.`,
                  color: "green"
                })
                this.$refs.form.resetValidation()
                this.$emit("close")
              })
              .catch((error) => {
                this.setNotify({
                  message: error && error.response.data ? error.response.data.message : `Something went wrong.`,
                  color: "red"
                })
              })
          }
        }
      },
      getCourse() {
        this.$axios.$get("api/v1/course").then(response => {
          this.userCourseChoices = response
        })
      },
      // batch details
      getBatch() {
        this.$axios.$get("api/v1/batch").then(response => {
          this.userBatchChoices = response
        })
      }
    }
  }
</script>

<style scoped></style>
