<template>
  <v-card class="mt-8">
    <v-card-title>
      <v-icon left v-text="'mdi-ballot-recount'" />
      <div class="blue-grey--text">
        Batch Lists
      </div>
      <v-spacer />
      <v-btn
        depressed
        color="blue-grey darken-2"
        class="white--text"
        @click="batchForm = true"
      >
        Create New
      </v-btn>
    </v-card-title>
    <v-card-text>
      <v-row>
        <v-col v-for="(batch, index) in batchDetails" :key="index" cols="4">
          <v-card flat class="blue-grey lighten-5">
            <v-card-title class="blue-grey--text">
              <div @click="$router.push(`/admin/student?selected_batch=${batch._id}`)" v-text="batch.batchName" />
              <v-spacer />
              <v-btn icon @click=";(formValues = batch), (batchForm = true)">
                <v-icon v-text="'mdi-pencil'" />
              </v-btn>
              <v-btn icon @click="deleteBatch(batch._id)">
                <v-icon v-text="'mdi-delete'" />
              </v-btn>
            </v-card-title>
            <v-card-text>
              <student-detail
                :endpoint="`api/v1/users/?batch=${batch._id}`"
              />
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-card-text>
    <v-dialog v-model="batchForm" width="600" persistent>
      <v-card>
        <v-card-title class="blue-grey--text">
          <v-icon left v-text="'mdi-file-move'" />
          Add new batch
        </v-card-title>
        <v-card-text class="">
          <div class="ma-4">
            <v-text-field v-model="formValues.batchCode" label="Batch Code" />
            <v-text-field v-model="formValues.batchName" label="Batch Name" />
          </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn
            text
            color="grey"
            class="text-capitalize"
            @click="batchForm = false"
          >
            Cancel
          </v-btn>
          <v-btn
            depressed
            color="blue-grey darken-2"
            class="white--text text-capitalize"
            @click="createBatch"
          >
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-card>
</template>
<script>
import StudentDetail from "../../components/HomePage/StudentDetail"
export default {
  components: { StudentDetail },
  data() {
    return {
      batchForm: false,
      batches: [
        {
          totalStudent: "21",
          totalTeacher: "21"
        },
        {
          totalTeacher: "21",
          totalStudent: "21"
        }
      ],
      formValues: {
        batchName: "",
        batchCode: ""
      },
      userCounts: [],
      batchDetails: []
    }
  },
  created() {
    this.getBatch()
  },
  mounted() {
    document.title = "Batch | Attendance management System"
  },
  methods: {
    getBatch() {
      this.$axios.get("api/v1/batch").then(response => {
        this.batchDetails = response.data
      })
    },
    deleteBatch(id) {
      this.$axios.$delete(`api/v1/batch/${id}/`).then(() => {
        this.setNotify({
          message: "Successfully remove Batch.",
          color: "green"
        })
        this.getBatch()
      })
    },
    createBatch() {
      if (this.formValues._id) {
        this.$axios
          .put(`api/v1/batch/${this.formValues._id}/`, this.formValues)
          .then(response => {
            this.setNotify({
              message: "Successfully updated Course.",
              color: "green"
            })
            this.batchForm = false
            this.getBatch()
          })
          .catch(() => {
            this.setNotify({
              message: "Something went wrong.",
              color: "red"
            })
          })
      } else {
        this.$axios
          .post("api/v1/batch", this.formValues)
          .then(response => {
            this.setNotify({
              message: "Successfully added batch.",
              color: "green"
            })
            this.batchForm = false
            this.getBatch()
          })
          .catch(() => {
            this.setNotify({
              message: "Something went wrong.",
              color: "red"
            })
          })
      }
    }
  }
}
</script>
