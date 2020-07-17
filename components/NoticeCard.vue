<template>
  <v-card>
    <v-card-title class="py-0">
      <user-detail :user="noticeDetail.createdBy" />
    </v-card-title>
    <v-divider class="my-0" />
    <v-card-text>
      <div>{{ noticeDetail.notice }}</div>
    </v-card-text>
  </v-card>
</template>
<script>
  import UserDetail from "../components/LayoutUtils/UserDetail"
  export default {
    components: { UserDetail },
    props: ["noticeDetail"],
    data() {
      return {
        user: {
          name: "Sandip Thapa",
          createdAt: "2019-01-23",
          image: "/logo.png"
        }
      }
    }
  }
</script>
