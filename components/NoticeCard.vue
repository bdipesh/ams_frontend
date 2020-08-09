<template>
  <v-card elevation="7">
    <v-card-title class="py-0">
      <user-detail :user="noticeDetail.createdBy" />
      <v-spacer></v-spacer>
      <v-btn v-if="noticeDetail.createdBy && ($auth.user._id === noticeDetail.createdBy._id || $auth.user.role === 'Admin')"
      icon @click="action = 'edit', message = noticeDetail.notice, noticeDelete = true" text>
      <v-icon
        :color="'blue'"
        v-text="'mdi-pencil'"></v-icon>
      </v-btn>
      <v-btn v-if="noticeDetail.createdBy && ($auth.user._id === noticeDetail.createdBy._id || $auth.user.role === 'Admin')"
             icon @click="action = 'delete', noticeDelete = true" text>
        <v-icon
          :color="'red'"
          v-text="'mdi-delete'"></v-icon>
      </v-btn>
      <v-btn icon @click="postLike" text>
        <v-icon
          :color="likedUser.includes($auth.user._id) ? 'blue' : ''"
          v-text="likedUser.includes($auth.user._id) ? 'mdi-thumb-up' : 'mdi-thumb-up-outline'"></v-icon>
      </v-btn>
    </v-card-title>
    <v-divider class="my-0" />
    <v-card-text>
      <div v-if="noticeDetail.notice.length > 300 && readMore" style="white-space: pre-wrap">
        {{ noticeDetail.notice.substring(0, 300) + '....'  }}</div>
      <div v-else style="white-space: pre-wrap">{{ noticeDetail.notice  }}</div>
    </v-card-text>
    <v-card-actions>
      <v-btn
        v-if="noticeDetail.notice.length > 500"
        color="red darken-4" small
        class="text-capitalize"
        @click="readMore = !readMore"
        text
      >
      {{  readMore ? 'Read more' : 'Show Less' }}
      </v-btn>
      <v-spacer></v-spacer>
      <v-btn color="red darken-4" small class="text-capitalize" @click="postLike" text>
        {{ noticeDetail.like}}
        Likes
      </v-btn>
      <v-btn  color="blue darken-4" @click="showComments = !showComments" text class="text-capitalize">
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
    <v-dialog
      v-model="noticeDelete"
      width="550"
    >
      <v-card v-if="noticeDelete">
        <v-card-title>
          Confirm Your Action
        </v-card-title>
        <v-card-text>
          <div v-if="action === 'delete'">
            Are You Sure You want to delete this notice.
          </div>
          <div v-else>
            <v-textarea
              v-model="message"
              label="Message"
            ></v-textarea>
          </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            text
            color="primary"
            @click="noticeDelete = false, message = '', action = '' "
          >
            Cancel
          </v-btn>
          <v-btn
            depressed
            color="primary"
            class="white--text text-capitalize"
            @click="confirmAction"
          >
            {{ action === 'delete' ? 'Yes' : 'Update' }}
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-card>
</template>
<script>
  import UserDetail from "../components/LayoutUtils/UserDetail"
  import CommentSection from "./HomePage/CommentSection";
  export default {
    components: {CommentSection, UserDetail },
    props: {
      noticeDetail: {
        type: Object,
        default () {
          return {
            action: '',
            deleteNotice: false,
            message: ''
          }
        }
      }
    },
    data() {
      return {
        showComments: false,
        noticeDelete: false,
        action: '',
        message: '',
        user: {
          name: "Sandip Thapa",
          createdAt: "2019-01-23",
          image: "/logo.png"
        },
        likedUser: [],
        readMore: false
      }
    },
    created () {
      this.noticeDetail.readMore = false
      if(this.noticeDetail.likedUser) {
        this.likedUser = [...this.noticeDetail.likedUser ]
      }
    },
    methods: {
      confirmAction () {
        if(this.action === 'delete') {
          this.deletePost()
        } else {
          this.updatePost()
        }
      },
      deletePost () {
        this.$axios.$delete(`api/v1/notice/${this.noticeDetail._id}`)
        .then((response) => {
          this.noticeDelete = false
          this.setNotify({message: 'Successfully Deleted Post.', color: 'green'})
          this.$emit('refresh')
        })
      },
      postLike () {
        let like = parseInt(this.noticeDetail.like )
        let likeUsers = []
        if(this.likedUser.includes(this.$auth.user._id)) {
          like = parseInt(this.noticeDetail.like || '0' ) - 1
          likeUsers = this.likedUser.splice(this.likedUser.indexOf(this.$auth.user._id), 1)
        } else {
          like = parseInt(this.noticeDetail.like || '0' ) + 1
          this.likedUser.push(`${this.$auth.user._id}`)
          likeUsers = [...this.likedUser]
        }
        const noticeData = {
          like: like,
          likedUser: likeUsers
        }
        this.$axios.$put(`/api/v1/notice/like/${this.noticeDetail._id}`, noticeData)
        .then((response)=> {
          this.$emit('refresh')
        })
      },
      updatePost() {
        let dataToPost = {
          notice: this.message
        }
        this.$axios.$put(`/api/v1/notice/${this.noticeDetail._id}`, dataToPost)
          .then((response)=> {
            this.$emit('refresh')
            this.noticeDelete = false
            this.setNotify({message: 'Successfully Update Post.', color: 'green'})
          })
      }

    }
  }
</script>
