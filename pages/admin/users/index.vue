<template>
  <v-card class="mt-10">
    <v-card-title>
      <v-icon left v-text="'mdi-account-group'" />
      <div class="blue-grey--text">
        User List
      </div>
      <v-spacer />
      <v-btn
        color="blue-grey darken-2"
        class="white--text"
        depressed
        @click="state.openUserForm = true"
      >
        Create New
      </v-btn>
    </v-card-title>
    <v-divider class="mt-0" />
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
          <td class="py-4">
            <v-menu nudge-bottom="40">
              <template v-slot:activator="{ on }">
                <v-btn icon v-on="on">
                  <v-icon v-text="'mdi-dots-vertical'" />
                </v-btn>
              </template>
              <v-list dense>
                <v-list-item>
                  <v-icon left v-text="'mdi-eye'" />
                  <v-list-item-title>
                    View Details
                  </v-list-item-title>
                </v-list-item>
                <v-list-item>
                  <v-icon left v-text="'mdi-pencil'" />
                  <v-list-item-title>
                    Edit Details
                  </v-list-item-title>
                </v-list-item>
                <v-list-item>
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
    <v-dialog
      v-model="state.openUserForm"
      scrollable
      @keypress.esc="state.openUserForm = false"
    >
      <add-new-user @close="getUserDetails(), (state.openUserForm = false)" />
    </v-dialog>
  </v-card>
</template>
<script>
  import { reactive, onMounted } from "@vue/composition-api"
  import AddNewUser from "../../../components/Users/AddNewUser"
  import UserDetail from "../../../components/LayoutUtils/UserDetail"
  export default {
    components: { UserDetail, AddNewUser },
    setup(_, { root: { $axios } }) {
      const headers = [
        { text: "Name", value: "name", sortable: false },
        { text: "Date of Birth", sortable: false },
        { text: "Email", sortable: false },
        { text: "Batch", sortable: false },
        { text: "Action", sortable: false }
      ]
      const state = reactive({
        userDetails: [],
        openUserForm: false
      })
      const getUserDetails = () => {
        $axios.$get("api/v1/users").then(response => {
          state.userDetails = [...response]
        })
      }
      onMounted(() => {
        getUserDetails()
      })
      return {
        getUserDetails,
        state,
        headers
      }
    }
  }
</script>
