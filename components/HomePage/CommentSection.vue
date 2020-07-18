<template>
  <v-card flat>
    <v-card-text>
      <v-row>
        <v-col
          v-for="(item, index) in commentDetails"
          :key="index"
          cols="12">
          <user-detail
            :user="item.userid"
          ></user-detail>
          <div class="px-10 black--text">
            {{ item.comment }}
          </div>
        </v-col>
        <v-col>
          <v-row dense align="center" class="ml-10">
            <v-col cols="auto">
              <v-text-field
                v-model="comment"
                label="Post Comment"
              ></v-text-field>
            </v-col>
            <v-col cols="auto">
              <v-btn
                depressed
                color="blue-grey"
                class="white--text text-capitalize"
                small
                @click="postComments"
              >
                Post
              </v-btn>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
    </v-card-text>
  </v-card>
</template>
<script>
import UserDetail from "../LayoutUtils/UserDetail";
export default {
  props: {
    noticeId: {
      type: String,
      required: true
    }
  },
  components: {UserDetail},
  data () {
    return {
      commentDetails: [],
      comment: ''
    }
  },
  created() {
    this.getCommentDetails()
  },
  methods: {
    getCommentDetails () {
      this.$axios.$get(`/api/v1/comment/?noticeId=${this.noticeId}`)
        .then((response) => {
          this.commentDetails = response
        })
    },
    postComments () {
      if(!this.comment) {
        this.setNotify({message: 'Please write something.', color: 'red'})
        return
      }
      let dataToPost = {
        noticeId: this.noticeId,
        comment: this.comment,
        userid: this.$auth.user._id
      }
      this.$axios.$post(`api/v1/comment/`, dataToPost)
      .then((response) => {
        this.setNotify({message: 'Successfully posted comment.', color: 'green'})
        this.getCommentDetails()
        this.comment = ''
      })
    }
  }
}
</script>
