<template>
  <v-card class="mt-8">
    <v-card-title>
      <v-row>
        <v-col md="6" sm="12">
          <v-col
            class="blue-grey--text title font-weight-bold py-0 mb-0"
            cols="12"
          >
            {{ `Attendance Report of ${this.$auth.user.name}` }}
          </v-col>
          <v-col cols="12" class="py-0">
            <div>
              <span class="body-2 font-weight-bold grey--text">Branch: </span>
              <span class="blue-grey--text body-2 font-weight-bold">{{
                "Septemeber"
              }}</span>
            </div>
          </v-col>
          <v-col cols="12" class="py-0">
            <v-row align="center" justify="center">
              <v-col class="grey--text body-2 font-weight-bold" cols="auto">
                Select Course
              </v-col>
              <v-col>
                <v-select
                  v-model="selectedCourse"
                  solo
                  hide-details
                  flat
                  clearable
                  label="Selected Course"
                  item-value="_id"
                  item-text="courseName"
                  :items="courseDetail"
                />
              </v-col>
            </v-row>
          </v-col>
        </v-col>
        <v-col cols="6" class="text-right">
          <v-row v-if="attendanceDetails.length" align="center" justify="end">
            <v-col cols="auto" class="">
              <v-progress-circular
                size="80"
                color="green"
                :value="totalPercent"
                width="8"
              >
                {{ totalPercent || "100" }}
              </v-progress-circular>
            </v-col>
            <v-col cols="auto">
              <div>
                <span class="grey--text body-2 font-weight-bold">
                  Total Classes :</span>
                <span class="blue-grey--text title font-weight-bold">
                  {{ totalClasses }}
                </span>
              </div>
              <div>
                <span class="grey--text body-2 font-weight-bold">
                  Total Present :</span>
                <span class="blue-grey--text title font-weight-bold">
                  {{ totalPresent }}
                </span>
              </div>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
    </v-card-title>
    <v-divider class="my-0" />
    <v-card-text class="px-0">
      <v-data-table :items="attendanceDetails" :headers="headers">
        <template slot="item" slot-scope="props">
          <tr>
            <td class="py-4">
              <div class="body-2 font-weight-bold">
                {{ getDate(props.item.date) }}
              </div>
              <div class="grey--text">
                {{ getDay(props.item.date) }}
              </div>
            </td>
            <td class="py-4">
              <v-chip
                :color="props.item.status === 'Present' ? 'green' : 'red'"
                class="white--text"
              >
                {{ props.item.status }}
              </v-chip>
            </td>
          </tr>
        </template>
        <template slot="no-data">
          <v-col class="text-center">
            Please Select Course To View Detail
          </v-col>
        </template>
      </v-data-table>
    </v-card-text>
  </v-card>
</template>
<script>
  import dayjs from "dayjs"
  export default {
    data() {
      return {
        selectedCourse: "",
        attendanceDetails: [],
        courseDetail: [],
        headers: [
          { text: "Date", sortable: false },
          { text: "Status", sortable: false }
        ]
      }
    },
    computed: {
      totalClasses() {
        return this.attendanceDetails.length
      },
      totalPresent() {
        return this.attendanceDetails.filter(x => x.status === "Present").length
      },
      totalPercent() {
        return (this.totalPresent / this.totalClasses) * 100
      }
    },
    watch: {
      selectedCourse() {
        if (this.selectedCourse) {
          this.getAttendanceDetails()
        }
      }
    },
    created() {
      this.getCourse()
    },
    methods: {
      getAttendanceDetails() {
        const queryParams = {
          student: this.$auth.user._id,
          course: this.selectedCourse || ""
        }
        this.$axios
          .$get("api/v1/attendance/student/", { params: queryParams })
          .then(response => {
            this.attendanceDetails = response
          })
      },
      getCourse() {
        this.$axios.$get("api/v1/course").then(response => {
          this.courseDetail = response
        })
      },
      getDate(date) {
        return dayjs(date).format("YYYY-MM-DD")
      },
      getDay(date) {
        return dayjs(date).format("dddd")
      }
    }
  }
</script>
