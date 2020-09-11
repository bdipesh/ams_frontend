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
        text
        color="blue-grey darken-2"
        class="white--text"
        depressed
        @click="importDialog = true"
      >
        Import
      </v-btn>
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
            <div
              v-if="!item.batch.includes(',')"
            >
              {{ state.batches.find(x => x._id === item.batch) ? state.batches.find(x => x._id === item.batch).batchName : ''}}
            </div>
            <div v-if="item.batch.includes(',')">
              <div
                v-for="(batch, index) in state.batches"
                :key="index"
              >
                <div v-if="item.batch.split(',').includes(batch._id)" >
                  {{ batch.batchName }}
                </div>
              </div>
            </div>
          </td>
          <td class="py-6">
            <div
              v-if="!item.course.includes(',')"
            >
              {{ state.courses.find(x => x._id === item.course) ? state.courses.find(x => x._id === item.course).courseName : ''}}
            </div>
            <div v-if="item.course.includes(',')">
              <div
                v-for="(course, index) in state.courses"
                :key="index"
              >
                <div class="my-2" v-if="item.course.split(',').includes(course._id)" >
                  <v-chip>
                    {{ course.courseName }}
                  </v-chip>
                </div>
              </div>
            </div>
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
                <v-list-item v-if="$auth.user.role === 'Teacher'" @click="userDetail = item, viewDetails = true">
                  <v-icon left v-text="'mdi-pencil'" />
                  <v-list-item-title>
                    View Details
                  </v-list-item-title>
                </v-list-item>
                <v-list-item v-if="$auth.user.role === 'Admin'" @click="deleteId = item._id, dialog = true">
                  <v-icon left v-text="'mdi-delete'" />
                  <v-list-item-title>
                    Remove Details
                  </v-list-item-title>
                </v-list-item>
              </v-list>
            </v-menu>
          </td>
        </tr>
      </template>
    </v-data-table>
    <v-dialog v-model="dialog" persistent max-width="500">
      <v-card v-if="dialog">
        <v-card-title class="headline blue-grey--text">Student Delete</v-card-title>
        <v-card-text>Are You Sure? </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" text @click="dialog = false">Cancel</v-btn>
          <v-btn color="red darken-3" depressed class="white--text" @click="deletesUserDetail()">Ok</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog
      v-model="viewDetails"
      width="960"

    >
      <profile-view
        v-if="viewDetails"
        :user-detail="userDetail"
        @close="viewDetails = false"
      ></profile-view>
    </v-dialog>
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
    <v-dialog
      v-model="importDialog"
      width="500"
    >
      <v-card v-if="importDialog">
        <v-card-title>
          Import Student
        </v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12">
              <v-select
                v-model="formValues.course"
                :items="userCourseChoices"
                :rules="requiredRules"
                item-text="courseName"
                item-value="_id"
                label="Course *"
                multiple
                @click="getCourse()"
              />
            </v-col>
            <v-col  cols="12">
              <v-select
                v-model="formValues.batch"
                :items="userBatchChoices"
                item-text="batchName"
                :rules="requiredRules"
                item-value="_id"
                label="Batch *"
                @click="getBatch()"
              />
            </v-col>
            <v-col  cols="12">
              <v-file-input
                v-model="formValues.importData"
                :rules="requiredRules"
                label="Select File *"
              />
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="primary"
            class="white--text"
            @click="importDialog = false"
          >Cancel </v-btn>
          <v-btn
            color="primary"
            class="white--text"
            @click="importStudent"
          >
           Import
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-card>
</template>
<script>
import { reactive, onMounted, computed } from "@vue/composition-api"
import AddNewUser from "../../../components/Users/AddNewUser"
import UserDetail from "../../../components/LayoutUtils/UserDetail"
import pageMixin from "../../../mixins/pageMixin";
import ProfileView from "../../../components/Users/ProfileView";
export default {
  data () {
    return {
      title: 'Student List | AMS',
      dialog: false,
      deleteId: '',
      viewDetails: false,
      importDialog: false,
      userDetail: {},
      formValues: {},
      userCourseChoices: [],
      userBatchChoices: [],
      requiredRules: [v => !!v || "This field is required"],
    }
  },
  mixins: [pageMixin],
  components: { UserDetail, ProfileView, AddNewUser },
  setup(_, { root: {$auth, $axios, $route } }) {
    const headers = [
      { text: "Name", value: "name", sortable: true },
      { text: "Date of Birth", value: 'date_of_birth', sortable: true },
      { text: "Email", value: 'email', sortable: true },
      { text: "Batch", sortable: false },
      { text: "Course", sortable: false },
      { text: "Action", sortable: false }
    ]
    const state = reactive({
      userDetails: [],
      openUserForm: false,
      actionData: {},
      selectedBatch: "",
      batches: {},
      courses: {}
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
    const getBatch = () => {
      $axios.$get(`api/v1/batch`)
        .then((response) => {
          state.batches = response
        })
    }
    const getCourse = () => {
      $axios.$get(`api/v1/course`)
        .then((response) => {
          state.courses = response
        })
    }
    const openProfileEditForm = detail => {
      state.actionData = detail
      state.openUserForm = true
    }
    onMounted(() => {
      state.selectedBatch = $route.query.selected_batch || ''
      getBatch()
      getCourse()
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
    deletesUserDetail () {
      this.$axios.$delete(`/api/v1/users/${this.deleteId}`)
      .then((response)=> {
        this.setNotify({message: 'Successfully deleted user.', color: 'green'})
        this.getUserDetails()
      })
      .catch((error) => {
        this.setNotify({message: 'Sorry something went wrong.', color: 'green'})
        this.getUserDetails()
      })
    },
    getCourse() {
      this.$axios.$get("api/v1/course").then(response => {
        this.userCourseChoices = response
      })
    },
    // batch details
    getBatch() {
      this.$axios.$get("api/v1/batch").then(response => {
        this.userBatchChoices = response
      })
    },
    importStudent() {
      const dataToPost =  new FormData()
      dataToPost.append('course', this.formValues.course.join(','))
      dataToPost.append('batch', this.formValues.batch)
      dataToPost.append('role', 'Student')
      dataToPost.append('files', this.formValues.importData)
      this.$axios.$post('api/v1/users/import/excel', dataToPost)
      .then((response) => {
        this.setNotify({message: 'Successfully Imported Data.', color: 'green'})
        this.getUserDetails()
        this.importDialog = false
        this.formValues = {}
      })
    }
  }
}
</script>
