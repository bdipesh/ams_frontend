<template>
  <v-card class="mt-8">
    <v-card-title>
      <v-icon left v-text="'mdi-ballot-recount'" />
      <div class="blue-grey--text">
        Course Lists
      </div>
      <v-spacer />
      <v-btn
        depressed
        color="blue-grey darken-2"
        class="white--text"
        @click="batchForm = true"
      >
        Create New
      </v-btn>
    </v-card-title>
    <v-card-text>
      <v-row>
        <v-col
          v-for="(batch, index) in batchDetails"
          :key="index"
          md="4"
          sm="12"
        >
          <v-card flat class="blue-grey lighten-5">
            <v-card-title class="blue-grey--text">
              <div v-text="batch.courseName" />
              <v-spacer />
              <v-btn icon @click=";(formValues = batch), (batchForm = true)">
                <v-icon v-text="'mdi-pencil'" />
              </v-btn>
              <v-btn icon @click="deleteBatch(batch._id)">
                <v-icon v-text="'mdi-delete'" />
              </v-btn>
            </v-card-title>
            <v-card-text>
              <student-detail
                :endpoint="`api/v1/users?course=${batch.courseCode}`"
              />
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-card-text>
    <v-dialog v-model="batchForm" width="600" persistent>
      <v-card>
        <v-card-title class="blue-grey--text">
          <v-icon left v-text="'mdi-file-move'" />
          Add new Course
        </v-card-title>
        <v-card-text class="">
          <div class="ma-4">
            <v-form ref="form" v-model="valid" :lazy-validation="lazy">
              <v-text-field
                v-model="formValues.courseCode"
                :rules="requiredRules"
                label="Course Code"
              />
              <v-text-field
                v-model="formValues.courseName"
                :rules="requiredRules"
                label="Course Name"
              />
            </v-form>
          </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn
            text
            color="grey"
            class="text-capitalize"
            @click="batchForm = false"
          >
            Cancel
          </v-btn>
          <v-btn
            depressed
            color="blue-grey darken-2"
            class="white--text text-capitalize"
            @click="createCourse"
          >
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-card>
</template>
<script>
  import StudentDetail from "../../components/HomePage/StudentDetail"
  export default {
    components: { StudentDetail },
    data() {
      return {
        batchForm: false,
        batches: [
          {
            totalStudent: "21",
            totalTeacher: "21"
          },
          {
            totalTeacher: "21",
            totalStudent: "21"
          }
        ],
        formValues: {
          courseName: "",
          courseCode: ""
        },
        valid: true,
        requiredRules: [v => !!v || "This field is required"],
        batchDetails: []
      }
    },
    created() {
      this.getCourse()
    },
    methods: {
      getCourse() {
        this.$axios.$get("api/v1/course").then(response => {
          this.batchDetails = response
        })
      },
      async getUserList(courseId) {
        let userList = []
        userList = await this.$axios.$get(`api/v1/users?course=${courseId}`)
        return {
          totalTeacher: userList.filter(x => x.role === "Teacher"),
          totalStudent: userList.filter(x => x.role === "Student")
        }
      },
      deleteBatch(id) {
        this.$axios.$delete(`api/v1/course/${id}/`).then(() => {
          this.setNotify({
            message: "Successfully remove Course.",
            color: "green"
          })
          this.getCourse()
        })
      },
      createCourse() {
        if (this.$refs.form.validate()) {
          if (this.formValues._id) {
            this.$axios
              .put(`api/v1/course/${this.formValues._id}/`, this.formValues)
              .then(() => {
                this.setNotify({
                  message: "Successfully updated Course.",
                  color: "green"
                })
                this.batchForm = false
                this.getCourse()
              })
              .catch(() => {
                this.setNotify("Something went wrong.")
              })
          } else {
            this.$axios
              .post("api/v1/course", this.formValues)
              .then(() => {
                this.setNotify({
                  message: "Successfully added Course.",
                  color: "green"
                })
                this.batchForm = false
                this.getCourse()
              })
              .catch(() => {
                this.setNotify({
                  message: "Something went wrong.",
                  color: "red"
                })
              })
          }
        }
      }
    }
  }
</script>
