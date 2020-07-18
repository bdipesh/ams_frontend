<template>
  <v-card>
    <v-card-title class="py-0">
      <user-detail :user="noticeDetail.createdBy" />
    </v-card-title>
    <v-divider class="my-0" />
    <v-card-text>
      <div>{{ noticeDetail.notice }}</div>
    </v-card-text>
    <v-divider></v-divider>
    <v-card-actions class="blue-grey lighten-5">
      <v-btn @click="postLike" text>
        {{ noticeDetail.like }}
        <v-icon v-text="'mdi-thumb-up-outline'"></v-icon>
      </v-btn>
      <v-spacer></v-spacer>
      <v-btn @click="showComments = !showComments" text class="text-capitalize">
        <v-icon left v-text="'mdi-comment-outline'"></v-icon>
        Comments
      </v-btn>
    </v-card-actions>
    <v-slide-y-transition>
      <comment-section
        v-if="showComments"
        :notice-id="noticeDetail._id"
      ></comment-section>
    </v-slide-y-transition>
  </v-card>
</template>
<script>
  import UserDetail from "../components/LayoutUtils/UserDetail"
  import CommentSection from "./HomePage/CommentSection";
  export default {
    components: {CommentSection, UserDetail },
    props: ["noticeDetail"],
    data() {
      return {
        showComments: false,
        user: {
          name: "Sandip Thapa",
          createdAt: "2019-01-23",
          image: "/logo.png"
        }
      }
    },
    computed: {

    },
    methods: {
      postLike () {
        let like = parseInt(this.noticeDetail.like || '0' )  + 1
        const noticeData = {
          like: like
        }
        this.$axios.$put(`/api/v1/notice/like/${this.noticeDetail._id}`, noticeData)
        .then((response)=> {
          this.$emit('refresh')
        })
      },
      getSum(x, y) {
        return x + y
      }
    }
  }
</script>
