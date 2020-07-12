<template>
  <v-card class="mt-8">
    <v-row align="center">
      <v-col md="6" sm="12">
        <v-col
          class="blue-grey--text title font-weight-bold py-0 mb-0"
          cols="12"
        >
          {{ `Select Course and Student for Attendance Report` }}
        </v-col>
        <v-col cols="12" class="py-0">
          <v-row align="center" justify="center">
            <v-col class="grey--text body-2 font-weight-bold" md="3" sm="12s">
              Select Batch
            </v-col>
            <v-col>
              <v-select
                v-model="selectedBatch"
                solo
                hide-details
                flat
                clearable
                label="Selected Batch"
                item-value="batchCode"
                item-text="batchName"
                :items="batchDetail"
              />
            </v-col>
          </v-row>
        </v-col>
        <v-col cols="12" class="py-0">
          <v-row align="center" justify="center">
            <v-col class="grey--text body-2 font-weight-bold" cols="3">
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
                item-value="courseCode"
                item-text="courseName"
                :items="courseDetail"
              />
            </v-col>
          </v-row>
        </v-col>
        <v-col cols="12" class="py-0">
          <v-row align="center" justify="center">
            <v-col class="grey--text body-2 font-weight-bold" cols="3">
              Select Student
            </v-col>
            <v-col>
              <v-select
                v-model="selectedStudent"
                solo
                hide-details
                flat
                clearable
                label="Selected Student"
                item-value="_id"
                item-text="name"
                :items="studentDetails"
              />
            </v-col>
          </v-row>
        </v-col>
      </v-col>
      <v-col v-if="attendanceDetails.length" cols="6" class="text-right">
        <v-row v-if="attendanceDetails.length" align="center" justify="center">
          <v-col cols="auto" class="">
            <v-progress-circular
              size="110"
              :color="getColor(totalPercent)"
              :value="totalPercent"
              width="12"
            >
              {{ totalPercent.toFixed(2) + "%" || "100" }}
            </v-progress-circular>
          </v-col>
          <v-col cols="auto">
            <div>
              <span class="grey--text body-2 font-weight-bold">
                Total Classes :</span
              >
              <span class="blue-grey--text title font-weight-bold">
                {{ totalClasses }}
              </span>
            </div>
            <div>
              <span class="grey--text body-2 font-weight-bold">
                Total Present :</span
              >
              <span class="blue-grey--text title font-weight-bold">
                {{ totalPresent }}
              </span>
            </div>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
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
import UserDetail from "../../components/LayoutUtils/UserDetail";
export default {
  components: {UserDetail},
  data() {
    return {
      selectedCourse: "",
      selectedBatch: "",
      attendanceDetails: [],
      courseDetail: [],
      selectedStudent: "",
      studentDetails: [],
      batchDetail: [],
      headers: [
        { text: "Date", align: "left", sortable: false },
        { text: "Status", align: "left", sortable: false }
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
    this.getStudent()
    this.getBatch()
  },
  methods: {
    getAttendanceDetails() {
      const queryParams = {
        student: this.selectedStudent,
        course: this.selectedCourse || ""
      }
      this.$axios
        .$get("api/v1/attendance/student/", { params: queryParams })
        .then(response => {
          this.attendanceDetails = response
        })
    },
    getColor(value) {
      if (value >= 80) {
        return "green"
      } else if (value >= 50) {
        return "orange"
      } else {
        return "red"
      }
    },
    getCourse() {
      this.$axios.$get("api/v1/course").then(response => {
        this.courseDetail = response
      })
    },
    getStudent() {
      this.$axios.$get("api/v1/users").then(response => {
        this.studentDetails = response.filter(x => x.role === "Student")
      })
    },
    getBatch() {
      this.$axios.$get("api/v1/batch").then(response => {
        this.batchDetail = response
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
<template>
  <v-card class="mt-8">
    <v-row align="center">
      <v-col md="6" sm="12">
        <v-col
          class="blue-grey--text title font-weight-bold py-0 mb-0"
          cols="12"
        >
          {{ `Select Course and Student for Attendance Report` }}
        </v-col>
        <v-col cols="12" class="py-0">
          <v-row align="center" justify="center">
            <v-col class="grey--text body-2 font-weight-bold" md="3" sm="12s">
              Select Batch
            </v-col>
            <v-col>
              <v-select
                v-model="selectedBatch"
                solo
                hide-details
                flat
                clearable
                label="Selected Batch"
                item-value="_id"
                item-text="batchName"
                :items="batchDetail"
              />
            </v-col>
          </v-row>
        </v-col>
        <v-col cols="12" class="py-0">
          <v-row align="center" justify="center">
            <v-col class="grey--text body-2 font-weight-bold" cols="3">
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
        <v-col cols="12" class="py-0">
          <v-row align="center" justify="center">
            <v-col class="grey--text body-2 font-weight-bold" cols="3">
              Select Student
            </v-col>
            <v-col>
              <v-select
                v-model="selectedStudent"
                solo
                hide-details
                flat
                clearable
                label="Selected Student"
                item-value="_id"
                item-text="name"
                :items="studentDetails"
              />
            </v-col>
          </v-row>
        </v-col>
      </v-col>
      <v-col v-if="attendanceDetails.length" cols="6" class="text-right">
        <v-row v-if="attendanceDetails.length" align="center" justify="center">
          <v-col cols="auto" class="">
            <v-progress-circular
              size="110"
              :color="getColor(totalPercent)"
              :value="totalPercent"
              width="12"
            >
              {{ totalPercent.toFixed(2) + "%" || "100" }}
            </v-progress-circular>
          </v-col>
          <v-col cols="auto">
            <div>
              <span class="grey--text body-2 font-weight-bold">
                Total Classes :</span
              >
              <span class="blue-grey--text title font-weight-bold">
                {{ totalClasses }}
              </span>
            </div>
            <div>
              <span class="grey--text body-2 font-weight-bold">
                Total Present :</span
              >
              <span class="blue-grey--text title font-weight-bold">
                {{ totalPresent }}
              </span>
            </div>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
    <v-divider class="my-0" />
    <v-card-text class="px-0">
      <v-data-table :items="attendanceDetails" :headers="headers">
        <template slot="item" slot-scope="props">
          <tr>
            <td>
              <user-detail :user="props.item.studentId" />
            </td>
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
      selectedBatch: "",
      attendanceDetails: [],
      courseDetail: [],
      selectedStudent: "",
      studentDetails: [],
      batchDetail: [],
      headers: [
        { text: "Student", sortable: false},
        { text: "Date", align: "left", sortable: false },
        { text: "Status", align: "left", sortable: false }
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
        this.getAttendanceDetails()
    },
    selectedStudent() {
        this.getAttendanceDetails()
    },
    selectedBatch() {
        this.getAttendanceDetails()
    }
  },
  created() {
    this.getCourse()
    this.getStudent()
    this.getBatch()
    this.getAttendanceDetails()
  },
  methods: {
    getAttendanceDetails() {
      const queryParams = {
        student: this.selectedStudent,
        course: this.selectedCourse || "",
        batch: this.selectedBatch || ''
      }
      this.$axios
        .$get("api/v1/attendance/student/", { params: queryParams })
        .then(response => {
          this.attendanceDetails = response.filter(x => x.studentId.role === 'Student')
        })
    },
    getColor(value) {
      if (value >= 80) {
        return "green"
      } else if (value >= 50) {
        return "orange"
      } else {
        return "red"
      }
    },
    getCourse() {
      this.$axios.$get("api/v1/course").then(response => {
        this.courseDetail = response
      })
    },
    getStudent() {
      this.$axios.$get("api/v1/users").then(response => {
        this.studentDetails = response.filter(x => x.role === "Student")
      })
    },
    getBatch() {
      this.$axios.$get("api/v1/batch").then(response => {
        this.batchDetail = response
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
