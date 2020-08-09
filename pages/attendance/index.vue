<template>
  <v-card class="mt-10">
    <v-card-title>
      <v-row align="" justify="">
        <v-col
          class="blue-grey--text title font-weight-bold py-0 mb-0"
          cols="12"
        >
          <v-row justify="center" align="center">
            <v-col class="py-0" cols="auto">
              <v-icon v-text="'mdi-fingerprint'" />
            </v-col>
            <v-col class="py-0">
              <div>
                <div>
                  {{ `Take Attendance` }}
                </div>
                <div class="grey--text body-2">
                  Please select batch and course to take attendance.
                </div>
              </div>
            </v-col>
          </v-row>
        </v-col>
        <v-col cols="12" class="py-0">
          <v-col md="5" sm="12" class="py-0">
            <v-row align="center" justify="center">
              <v-col
                class="grey--text body-2 py-0 font-weight-bold"
                cols="auto"
              >
                Select Batch
              </v-col>
              <v-col class="py-0">
                <v-select
                  v-model="filters.batch"
                  :items="userBatchChoices"
                  item-text="batchName"
                  item-value="_id"
                  clearable
                  solo
                  flat
                  label="Active Batch"
                  hide-details
                />
              </v-col>
            </v-row>
          </v-col>
        </v-col>
        <v-col cols="12" class="py-0">
          <v-col md="5" sm="12" class="py-0">
            <v-row align="center" justify="center">
              <v-col
                class="grey--text body-2 font-weight-bold py-0"
                cols="auto"
              >
                Select Course
              </v-col>
              <v-col class="py-0">
                <v-select
                  v-model="filters.course"
                  :items="userCourseChoices"
                  item-text="courseName"
                  item-value="_id"
                  clearable
                  solo
                  flat
                  label="Active Course"
                  hide-details
                />
              </v-col>
            </v-row>
          </v-col>
        </v-col>
      </v-row>

    </v-card-title>
    <v-divider class="mt-0" />
    <v-card-text class="pt-0">
      <v-alert v-if="!filters.course || !filters.batch" class="my-5" type="success">
        Please select branch and course to take attendance.
      </v-alert>
      <v-row v-else>
        <v-col cols="12">
          <v-card
            v-for="(user, index) in userDetail"
            :key="index"
            class="mb-5 blue-grey lighten-5"
            outlined
          >
            <v-row class="px-5">
              <v-col cols="6" class="py-0">
                <user-detail :user="user" />
              </v-col>
              <v-col cols="6" class="py-0">
                <v-radio-group v-model="user.status">
                  <v-row>
                    <v-col>
                      <v-radio value="Present" label="Present" />
                    </v-col>
                    <v-col>
                      <v-radio value="Absent" label="Absent" />
                    </v-col>
                  </v-row>
                </v-radio-group>
              </v-col>
            </v-row>
          </v-card>
        </v-col>
      </v-row>
    </v-card-text>
    <v-card-actions>
      <v-spacer />
      <v-btn
        v-if="filters.course || filters.batch"
        depressed
        color="blue-grey"
        class="white--text"
        @click="saveAllAttendance"
      >
        Save
      </v-btn>
    </v-card-actions>
  </v-card>
</template>
<script>
  import UserDetail from "../../components/LayoutUtils/UserDetail"
  import pageMixin from "../../mixins/pageMixin";
  export default {
    components: { UserDetail },
    mixins: [pageMixin],
    data() {
      return {
        title: 'Attendance | AMS',
        filters: {
          course: "",
          batch: ""
        },
        userCourseChoices: "",
        userBatchChoices: "",
        userDetail: []
      }
    },
    computed: {
      queryParams() {
        return {
          course: this.filters.course || "",
          batch: this.filters.batch || ""
        }
      }
    },
    watch: {
      queryParams() {
        this.getUserDetail()
      }
    },
    created() {
      this.getUserDetail()
      this.getCourse()
      this.getBatch()
    },
    methods: {
      getUserDetail() {
        this.$axios
          .$get("api/v1/users", { params: this.queryParams })
          .then(response => {
            this.userDetail = response.filter(x => x.role === "Student")
          })
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
      },
      saveAllAttendance() {
        this.userDetail.forEach((details, index) => {
          if(details.attendance_id) {
            this.updateAttendance(details)
          } else {
            this.saveAttendance(details, index)
          }
        })
      },
      saveAttendance(details, index) {
        const dataToPost = {
          student: details._id,
          course: this.filters.course,
          batch: this.filters.batch,
          status: details.status
        }
        this.$axios
          .$post("api/v1/attendance", dataToPost)
          .then((response) => {
            this.userDetail[index].attendance_id = response._id
            this.setNotify({
              message: "Successfully taken attendance",
              color: "green"
            })
          })
          .catch(() => {
            this.setNotify({ message: "Sorry", color: "green" })
          })
      },
      updateAttendance(details, id) {
        const dataToPost = {
          student: details._id,
          course: this.filters.course,
          batch: this.filters.batch,
          status: details.status
        }
        this.$axios
          .$put(`api/v1/attendance/${details.attendance_id}/`, dataToPost)
          .then(() => {
            this.setNotify({
              message: "Successfully taken attendance",
              color: "green"
            })
          })
          .catch(() => {
            this.setNotify({ message: "Sorry", color: "green" })
          })
      }
    }
  }
</script>
