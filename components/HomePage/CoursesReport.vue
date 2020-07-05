<template>
  <v-card class="mt-4">
    <v-card-title>
      <div class="title font-weight-bold">
        <v-icon left v-text="'mdi-card-plus'" />
        <span class="blue-grey--text font-weight-bold">
         My Courses
        </span>
      </div>
    </v-card-title>
    <v-card-text>
      <v-row>
        <v-col v-for="(course, index) in courses" :key="index" cols="6">
          <course-detail
            :course-code="course.courseCode"
            :course-name="course.courseName"
          />
        </v-col>
      </v-row>
    </v-card-text>
  </v-card>
</template>
<script>
import CourseDetail from "./courseDetail"
export default {
  components: { CourseDetail },
  data() {
    return {
      courses: [],
      attendanceResult: []
    }
  },
  created() {
    this.getCourses()
  },
  methods: {
    getCourses() {
      this.$axios.$get("api/v1/course").then(response => {
        this.courses = response
      })
    }
  }
}
</script>
