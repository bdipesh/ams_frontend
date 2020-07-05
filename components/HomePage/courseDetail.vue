<template>
  <v-card flat class="blue-grey lighten-5">
    <v-card-title class="blue-grey--text">
      <div v-text="courseName" />
    </v-card-title>
    <v-card-text>
      <div>
        <v-icon v-text="'mdi-account'" />
        <span>Total Class: </span>
        <b>{{ totalClasses }}</b>
      </div>
      <div>
        <v-icon v-text="'mdi-account'" />
        <span>Total Present: </span>
        <b>{{ totalPresent }}</b>
      </div>
    </v-card-text>
  </v-card>
</template>
<script>
export default {
  props: ["courseCode", "courseName"],
  data() {
    return {
      attendanceResult: []
    }
  },
  computed: {
    totalClasses() {
      return this.attendanceResult.length
    },
    totalPresent() {
      return this.attendanceResult.filter(x => x.status === "Present").length
    },
    totalPercent() {
      return (this.totalPresent / this.totalClasses) * 100
    }
  },
  created() {
    this.getAttendanceDetails()
  },
  methods: {
    async getAttendanceDetails() {
      const queryParams = {
        student: this.$auth.user._id,
        course: this.courseCode || ""
      }
      await this.$axios
        .$get("api/v1/attendance/student/", { params: queryParams })
        .then(response => {
          this.attendanceResult = response
        })
    }
  }
}
</script>
