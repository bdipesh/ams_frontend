<template>
  <profile-view
    :user-detail="userDetail"
    is-profile
  ></profile-view>
</template>
<script>

import AddNewUser from "../components/Users/AddNewUser"
import pageMixin from "../mixins/pageMixin";
import ProfileView from "../components/Users/ProfileView";
export default {
  mixins: [pageMixin],
  components: { AddNewUser, ProfileView },
  data() {
    return {
      title:'My Profile | AMS',
      openProfileEditForm: false,
      userDetail: {}
    }
  },
  created() {
    this.getUserDetail()
  },
  methods: {
    getUserDetail() {
      this.$axios
        .$get(`api/v1/users/${this.userId || this.$auth.user._id}`)
        .then(response => {
          this.userDetail = response
        })
    }
  }
}
</script>
