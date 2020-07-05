<template>
  <div>
    <div>
      <v-icon v-text="'mdi-account'" />
      <span>Total Student: </span>
      <b>{{ studentCount }}</b>
    </div>
    <div>
      <v-icon v-text="'mdi-account'" />
      <span>Total Teacher: </span>
      <b>{{ teacherCount }}</b>
    </div>
  </div>
</template>
<script>
  export default {
    props: ["endpoint"],
    data() {
      return {
        studentCount: "",
        teacherCount: ""
      }
    },
    created() {
      this.$axios.$get(this.endpoint).then(response => {
        this.studentCount = response.filter(x => x.role === "Student").length
        this.teacherCount = response.filter(x => x.role === "Teacher").length
      })
    }
  }
</script>
