<template>
  <v-row>
    <v-col md="6" xs="12" sm="12">
      <v-row align="center">
        <!--        <v-col cols="12">-->
        <!--          <post-notice class="mb-3" @close="getNotices" />-->
        <!--        </v-col>-->
        <!--        <template>-->
        <!--          <v-col-->
        <!--            v-for="(notice, index) in noticeDetails"-->
        <!--            :key="`${index}`"-->
        <!--            cols="12"-->
        <!--          >-->
        <!--            <notice-card :notice-detail="notice" />-->
        <!--          </v-col>-->
        <!--        </template>-->
        <v-col class="text-center" cols="1">
          <div>
            No Notice Posted
          </div>
        </v-col>
      </v-row>
    </v-col>

    <v-col cols="6">
      <courses-report />
    </v-col>
  </v-row>
</template>

<script>
// import NoticeCard from "../components/NoticeCard"
import CoursesReport from "../components/HomePage/CoursesReport"
// import AttendanceActivity from "../components/HomePage/CoursesReport"
import PostNotice from "../components/HomePage/PostNotice"
import pageMixin from "../mixins/pageMixin";
export default {
  mixins: [pageMixin],
  components: {
    // NoticeCard,
    CoursesReport,
    PostNotice,
    // AttendanceActivity
  },
  data() {
    return {
      title:'Notice | AMS',
      noticeDetails: ""
    }
  },
  mounted() {
    this.getNotices()
  },
  methods: {
    getNotices() {
      this.$axios.$get(`api/v1/notice`).then(response => {
        this.noticeDetails = response
      })
    }
  }
}
</script>
