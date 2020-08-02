<template>
  <v-card class="mt-8">
    <v-card-title>
      <user-detail :user="userDetail" />
      <v-spacer />
      <v-btn
        v-if="isProfile"
        depressed
        color="blue-grey darken-2 text-capitalize"
        class="white--text"
        @click="openProfileEditForm = true"
      >
        <v-icon left v-text="'mdi-pencil'" />
        Update Profile
      </v-btn>
      <v-btn
        v-if="!isProfile"
        text
        class="text-capitalize"
        @click="$emit('close')"
      >
       Cancel
      </v-btn>
    </v-card-title>
    <v-card-text>
      <v-row>
        <v-col cols="6">
          <v-col class="font-weight-bold subtitle py-0 blue-grey--text">
            <v-icon left v-text="'mdi-account'" />
            Personal Details
          </v-col>
          <v-row>
            <v-col cols="6">
              <v-row align="center">
                <v-col cols="auto">
                  <v-icon v-text="'mdi-phone'" />
                </v-col>
                <v-col cols="auto">
                  <div class="grey--text">
                    Phone
                  </div>
                  <div class="blue-grey--text">
                    {{ userDetail.phone }}
                  </div>
                </v-col>
                <v-col />
              </v-row>
            </v-col>
            <v-col cols="6">
              <v-row align="center">
                <v-col cols="auto">
                  <v-icon v-text="'mdi-message'" />
                </v-col>
                <v-col cols="auto">
                  <div class="grey--text">
                    Email
                  </div>
                  <div class="blue-grey--text">
                    {{ userDetail.email }}
                  </div>
                </v-col>
                <v-col />
              </v-row>
            </v-col>
            <v-col cols="6">
              <v-row align="center">
                <v-col cols="auto">
                  <v-icon v-text="'mdi-calendar'" />
                </v-col>
                <v-col cols="auto">
                  <div class="grey--text">
                    Date of Birth
                  </div>
                  <div class="blue-grey--text">
                    {{ userDetail.dob }}
                  </div>
                </v-col>
                <v-col />
              </v-row>
            </v-col>
            <v-col cols="6">
              <v-row align="center">
                <v-col cols="auto">
                  <v-icon v-text="'mdi-gender-male-female'" />
                </v-col>
                <v-col cols="auto">
                  <div class="grey--text">
                    Gender
                  </div>
                  <div class="blue-grey--text">
                    {{ userDetail.gender }}
                  </div>
                </v-col>
                <v-col />
              </v-row>
            </v-col>
          </v-row>
        </v-col>
        <v-col cols="6">
          <v-col class="font-weight-bold subtitle py-0 blue-grey--text">
            <v-icon left v-text="'mdi-firebase'" />
            Academic Details
          </v-col>
          <v-row>
            <v-col cols="6">
              <v-row align="center">
                <v-col cols="auto">
                  <v-icon v-text="'mdi-phone'" />
                </v-col>
                <v-col cols="auto">
                  <div class="grey--text">
                    Phone
                  </div>
                  <div class="blue-grey--text">
                    {{ userDetail.phone }}
                  </div>
                </v-col>
                <v-col />
              </v-row>
            </v-col>
            <v-col cols="6">
              <v-row align="center">
                <v-col cols="auto">
                  <v-icon v-text="'mdi-message'" />
                </v-col>
                <v-col cols="auto">
                  <div class="grey--text">
                    Email
                  </div>
                  <div class="blue-grey--text">
                    {{ userDetail.email }}
                  </div>
                </v-col>
                <v-col />
              </v-row>
            </v-col>
            <v-col cols="6">
              <v-row align="center">
                <v-col cols="auto">
                  <v-icon v-text="'mdi-calendar'" />
                </v-col>
                <v-col cols="auto">
                  <div class="grey--text">
                    Date of Birth
                  </div>
                  <div class="blue-grey--text">
                    {{ userDetail.dob }}
                  </div>
                </v-col>
                <v-col />
              </v-row>
            </v-col>
            <v-col cols="6">
              <v-row align="center">
                <v-col cols="auto">
                  <v-icon v-text="'mdi-gender-male-female'" />
                </v-col>
                <v-col cols="auto">
                  <div class="grey--text">
                    Gender
                  </div>
                  <div class="blue-grey--text">
                    {{ userDetail.gender }}
                  </div>
                </v-col>
                <v-col />
              </v-row>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
    </v-card-text>
    <v-dialog
      v-model="openProfileEditForm"
      width="960"
      scrollable
      persistent
      @keypress.esc="openProfileEditForm = false"
    >
      <add-new-user
        :action-data="userDetail"
        update
        :title="'Update Your Details'"
        @close="openProfileEditForm = false"
      />
    </v-dialog>
  </v-card>
</template>
<script>
import UserDetail from "../LayoutUtils/UserDetail"
export default {
  components: {
    UserDetail
  },
  props: {
    userDetail: {
      type: Object,
      default () {
        return {}
      }
    },
    isProfile: {
      type: Boolean,
      default: false
    }
  }
}
</script>
