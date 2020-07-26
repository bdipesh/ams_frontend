<template>
  <v-card class="mt-10">
    <v-card-title>
      <v-icon left v-text="'mdi-account-group'" />
      <div class="blue-grey--text">
        Student List
      </div>
      <v-spacer />
      <v-btn
        v-if="$auth.user.role==='Admin'"
        color="blue-grey darken-2"
        class="white--text"
        depressed
        @click="state.openUserForm = true"
      >
        Create New
      </v-btn>
    </v-card-title>
    <v-divider class="mt-0" />
    <v-row>
      <v-col />
      <v-col />
      <v-col />
    </v-row>
    <v-data-table
      :items="state.userDetails"
      :headers="headers"
      dense
      align="center"
    >
      <template v-slot:body="{ items }">
        <tr v-for="(item, index) in items" :key="index">
          <td class="">
            <user-detail :user="item" />
          </td>
          <td class="py-6">
            {{ item.dob }}
          </td>
          <td class="py-6">
            {{ item.email }}
          </td>
          <td class="py-6">
            {{ item.batch }}
          </td>
          <td  class="py-4">
            <v-menu nudge-bottom="40">
              <template v-slot:activator="{ on }">
                <v-btn icon v-on="on">
                  <v-icon v-text="'mdi-dots-vertical'" />
                </v-btn>
              </template>
              <v-list dense>
                <!--                <v-list-item>-->
                <!--                  <v-icon left v-text="'mdi-eye'" />-->
                <!--                  <v-list-item-title>-->
                <!--                    View Details-->
                <!--                  </v-list-item-title>-->
                <!--                </v-list-item>-->
                <v-list-item v-if="$auth.user.role === 'Admin'" @click="openProfileEditForm(item)">
                  <v-icon left v-text="'mdi-pencil'" />
                  <v-list-item-title>
                    Edit Details
                  </v-list-item-title>
                </v-list-item>
                <v-list-item v-if="$auth.user.role === 'Teacher'" @click="openProfileEditForm(item)">
                  <v-icon left v-text="'mdi-pencil'" />
                  <v-list-item-title>
                    View Details
                  </v-list-item-title>
                </v-list-item>
                <v-list-item v-if="$auth.user.role === 'Admin'" @click="dialog = true">
                  <v-icon left v-text="'mdi-delete'" />
                  <v-list-item-title>
                    Remove Details
                  </v-list-item-title>
                  <v-row justify="center">
                    <v-dialog v-model="dialog" persistent max-width="290">
                      <v-card>
                        <v-card-title class="headline">Student Delete</v-card-title>
                        <v-card-text>Are You Sure? </v-card-text>
                        <v-card-actions>
                          <v-spacer></v-spacer>
                          <v-btn color="green darken-1" text @click="dialog = false">Cancel</v-btn>
                          <v-btn color="green darken-1" text @click="deletesUserDetail(item._id)">Ok</v-btn>
                        </v-card-actions>
                      </v-card>
                    </v-dialog>
                  </v-row>
                </v-list-item>
              </v-list>
            </v-menu>
          </td>
        </tr>
      </template>
    </v-data-table>
    <v-dialog
      v-model="state.openUserForm"
      scrollable
      width="900"
      @keypress.esc="state.openUserForm = false"
    >
      <add-new-user
        v-if="state.openUserForm"
        title="Add New User"
        :action-data="state.actionData"
        :as="null"
        :selected-batch="state.selectedBatch"
        @close="
          getUserDetails(),
          (state.actionData = {}),
          (state.openUserForm = false)
        "
      />
    </v-dialog>
  </v-card>
</template>
<script>
import { reactive, onMounted, computed } from "@vue/composition-api"
import AddNewUser from "../../../components/Users/AddNewUser"
import UserDetail from "../../../components/LayoutUtils/UserDetail"
import pageMixin from "../../../mixins/pageMixin";
export default {
  data () {
    return {
      title: 'Student List | AMS',
      dialog: false,
      deleteId: ''
    }
  },
  mixins: [pageMixin],
  components: { UserDetail, AddNewUser },
  setup(_, { root: {$auth, $axios, $route } }) {
    const headers = [
      { text: "Name", value: "name", sortable: true },
      { text: "Date of Birth", sortable: false },
      { text: "Email", sortable: false },
      { text: "Batch", sortable: false },
      { text: "Action", sortable: false }
    ]
    const state = reactive({
      userDetails: [],
      openUserForm: false,
      actionData: {},
      selectedBatch: ""
    })
    const getUserDetails = () => {
      $axios
        .$get(
          `api/v1/users/?batch=${$route.query.selected_batch ||
          state.selectedBatch}&role=Student`
        )
        .then(response => {
          state.userDetails = [...response]
        })
    }
    const openProfileEditForm = detail => {
      state.actionData = detail
      state.openUserForm = true
    }
    onMounted(() => {
      state.selectedBatch = $route.query.selected_batch || ''
      getUserDetails()
      if($auth.user.role === 'Teacher') {
        headers.pop()
      }
    })
    return {
      openProfileEditForm,
      getUserDetails,
      state,
      headers
    }
  },
  methods: {
    deletesUserDetail (deleteId) {
      this.$axios.$delete(`/api/v1/users/${deleteId}`)
      .then((response)=> {
        this.setNotify({message: 'Successfully deleted user.', color: 'green'})
        this.getUserDetails()
      })
      .catch((error) => {
        this.setNotify({message: 'Sorry something went wrong.', color: 'green'})
        this.getUserDetails()
      })
    }
  }
}
</script>
