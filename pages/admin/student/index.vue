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
        @click="openUserForm = true"
      >
        Create New
      </v-btn>
    </v-card-title>
    <v-divider class="mt-0" />
    <v-row class="mx-1">
      <v-col>
        <v-select
          v-model="selectedBatch"
          :items="batches"
          item-value="_id"
          clearable
          item-text="batchName"
          label="Filter By Branch"
        ></v-select>
      </v-col>
      <v-col>
        <v-select
          v-model="selectedCourse"
          :items="courses"
          clearable
          item-value="_id"
          item-text="courseName"
          label="Filter by Course."
        ></v-select>
      </v-col>
      <v-col />
    </v-row>
    <v-data-table
      :items="userDetails"
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
              {{ batches.find(x => x._id === item.batch) ? batches.find(x => x._id === item.batch).batchName : ''}}
            </div>
            <div v-if="item.batch.includes(',')">
              <div
                v-for="(batch, index) in batches"
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
              {{ courses.find(x => x._id === item.course) ? courses.find(x => x._id === item.course).courseName : ''}}
            </div>
            <div v-if="item.course.includes(',')">
              <div
                v-for="(course, index) in courses"
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
      v-model="openUserForm"
      scrollable
      width="900"
      @keypress.esc="openUserForm = false"
    >
      <add-new-user
        v-if="openUserForm"
        title="Add New User"
        :action-data="actionData"
        :as="null"
        :selected-batch="selectedBatch"
        @close="
          getUserDetails(),
          (actionData = {}),
          (openUserForm = false)
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
  mixins: [pageMixin],
  components: { UserDetail, ProfileView, AddNewUser },
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
      headers:[
        { text: "Name", value: "name", sortable: true },
        { text: "Date of Birth", value: 'date_of_birth', sortable: true },
        { text: "Email", value: 'email', sortable: true },
        { text: "Batch", sortable: false },
        { text: "Course", sortable: false },
        { text: "Action", sortable: false }
      ],
      userDetails: [],
      openUserForm: false,
      actionData: {},
      selectedBatch: "",
      selectedCourse: '',
      batches: {},
      courses: {},
      userBatchChoices: [],
      requiredRules: [v => !!v || "This field is required"],
    }
  },
  watch: {
    selectedBatch () {
      this.getUserDetails()
    },
    selectedCourse () {
      this.getUserDetails()
    }
  },
  mounted () {
    if(this.$auth.user.role === 'Teacher') {
      this.headers.pop()
    }
    this.getBatch()
    this.getCourse()
    this.getUserDetails()
  },
  methods: {
    getUserDetails () {
      this.$axios
        .$get(
          `api/v1/users/?batch=${this.$route.query.selected_batch ||
          this.selectedBatch || ''}&course=${this.selectedCourse || ''}&role=Student`
        )
        .then(response => {
          this.userDetails = [...response]
        })
    },
     getBatch  ()  {
      this.$axios.$get(`api/v1/batch`)
        .then((response) => {
          this.batches = response
          this.userBatchChoices = response

        })
    },
     getCourse  ()  {
      this.$axios.$get(`api/v1/course`)
        .then((response) => {
          this.courses = response
          this.userCourseChoices = response
        })
    },
    openProfileEditForm ( detail) {
      this.actionData = detail
      this.openUserForm = true
    },
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
