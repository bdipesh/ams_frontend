<template>
  <v-card class="mt-4">
    <v-card-title>
      <div class="title font-weight-bold">
        <v-icon left v-text="'mdi-card-plus'" />
        <span class="blue-grey--text font-weight-bold">
        {{ $auth.user.role === 'Admin' ? 'All Courses' : 'My Courses' }}
        </span>
      </div>
    </v-card-title>
    <v-card-text>
      <v-row>
        <v-col v-for="(course, index) in courses" :key="index" cols="6">
          <course-detail
            :course-code="course._id"
            :course-name="course.courseName"
          />
        </v-col>
      </v-row>
<!--      <v-row v-else>-->
<!--        <v-col v-for="(course, index) in courses" :key="index" cols="6">-->
<!--          <div v-if="courses.includes(course._id)">-->
<!--            <course-detail-->
<!--              :course-code="course._id"-->
<!--              :course-name="coursesList.find(x => x._id === course) ? coursesList.find(x => x._id === course).courseName : ''"-->
<!--            />-->
<!--          </div>-->
<!--        </v-col>-->
<!--      </v-row>-->
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
      attendanceResult: [],
      coursesList: []
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
    },
    getCoursesList() {
      this.$axios.$get("api/v1/course").then(response => {
        this.coursesList = response
      })
    }
  }
}
</script>
