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
        id="addBatchButton"
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
              <v-btn icon @click=";(formValues = batch), (batchForm = true),(update=true)" v-bind:id="batch.batchName">
                <v-icon v-text="'mdi-pencil'" />
              </v-btn>
              <v-btn icon @click="dialog=true" v-bind:id="batch.batchCode">
                <v-icon v-text="'mdi-delete'" />
                <v-row justify="center">
                  <v-dialog v-model="dialog" persistent max-width="290">
                    <v-card>
                      <v-card-title class="headline">Batch Delete</v-card-title>
                      <v-card-text>Are You Sure? </v-card-text>
                      <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn color="green darken-1" text @click="dialog = false">Cancel</v-btn>
                        <v-btn color="green darken-1" text @click="deleteBatch(batch._id)">Ok</v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                </v-row>
              </v-btn>
            </v-card-title>
            <v-card-text>
              <student-detail
                :endpoint="`api/v1/users/?batch=${batch.batchCode}`"
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
          {{ update ? 'Update Details' : 'Add New Batch' }}
        </v-card-title>
        <v-card-text class="">
          <div class="ma-4">
            <v-text-field v-model="formValues.batchCode" label="Batch Code" id="batchCode" />
            <v-text-field v-model="formValues.batchName" label="Batch Name" id="batchName" />
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
            id="saveAddBatch"
          >
            {{ update ? 'Update' : 'Save' }}
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-card>

</template>
<script>
import StudentDetail from "../../components/HomePage/StudentDetail"
import pageMixin from "../../mixins/pageMixin";
export default {
  components: { StudentDetail },
  mixins: [pageMixin],
  update: {
    type: Boolean,
    default: false
  },
  data() {
    return {
      title: 'Batch | AMS',
      batchForm: false,
      dialog: false,
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
  methods: {
    getBatch() {
      this.$axios.get("api/v1/batch").then(response => {
        this.batchDetails = response.data
      })
    },
    deleteBatch(id) {
      this.dialog=false;
      this.$axios.$delete(`api/v1/batch/${id}/`).then(() => {
        this.setNotify({
          message: "Successfully removed Batch.",
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
              message: "Successfully updated Batch.",
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
              message: "Successfully added Batch.",
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
