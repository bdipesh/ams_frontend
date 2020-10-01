<template>
  <v-app>
    <v-app-bar fixed color="#18c0ff" dark>
      <v-app-bar-nav-icon @click.stop="leftDrawer = !leftDrawer" />
      <v-toolbar-title class="mr-3" v-text="title" />
      <v-spacer/>
      <v-chip
        v-text="$auth.user.role"
        color="white"
        class="black--text"
      ></v-chip>
      <v-spacer/>
      <v-menu bottom nudge-bottom="55">
        <template v-slot:activator="{ on }">
            <v-col cols="auto">
              <v-row v-on="on" v-if="$auth.loggedIn" align="center">
                <v-col cols="auto" class="mr-3">
                  <v-avatar size="50">
                    <v-img :src="'http://localhost:8000/static/' + $auth.user.picture" />
                  </v-avatar>
                </v-col>
                <v-col class="pr-0" cols="auto">
                  <v-list-item-title class="white--text font-weight-bold">
                    {{ $auth.user.name }}
                  </v-list-item-title>
                </v-col>
                <v-col class="px-0" cols="auto">
                  <v-icon v-text="'mdi-chevron-down'"></v-icon>
                </v-col>
              </v-row>
            </v-col>
        </template>
        <v-list dense>
          <v-list-item @click="logout">
            <v-list-item-icon>
              <v-icon v-text="'mdi-lock'" />
            </v-list-item-icon>
            <v-list-item-title>
              Logout
            </v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>
    <v-navigation-drawer
      v-model="leftDrawer"
      color="blue-grey darken-4"
      fixed
      clipped
      app
      bottom
      left
    >
      <v-list class="mt-12 pt-8" dense nav>
        <template v-for="(item, index) in items">
          <v-list-item
            v-if="item.permission.includes($auth.user.role)"
            :key="index"
            link
            :to="item.to"
          >
            <v-list-item-icon>
              <v-icon color="white">{{ item.icon }}</v-icon>
            </v-list-item-icon>

            <v-list-item-content>
              <v-list-item-title class="white--text font-weight-bold">
                {{ item.title }}
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </template>
      </v-list>
    </v-navigation-drawer>
    <v-content>
      <v-container class="mt-9">
        <nuxt />
      </v-container>
    </v-content>
    <vue-snackbar />
    <v-footer :fixed="fixed" app>
      <span>&copy; 2020 AMS</span>
    </v-footer>
  </v-app>
</template>

<script>
import VueSnackbar from "../components/LayoutUtils/VueSnackbar"
export default {
  components: { VueSnackbar },
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: "mdi-monitor-dashboard",
          title: "Dashboard",
          to: "/",
          permission: ["Admin", "Student", "Teacher"]
        },
        {
          icon: "mdi-ballot-recount",
          title: "My Courses",
          to: "/admin/course",
          permission: ["Admin"]
        },
        {
          icon: "mdi-account",
          title: "Admin User",
          to: "/admin/admin-user",
          permission: ["Admin"]
        },
        {
          icon: "mdi-ballot-recount",
          title: "Take Attendance",
          to: "/attendance",
          permission: ["Teacher","Admin"]
        },
        {
          icon: "mdi-account-plus",
          title: "Teachers",
          to: "/admin/teacher",
          permission: ["Admin", "Student"]
        },
        {
          icon: "mdi-fingerprint",
          title: "Attendance Report",
          to: "/report/attendance",
          permission: ["Admin", "Teacher"]
        },
        {
          icon: "mdi-fingerprint",
          title: "View Attendance",
          to: "/user-attendance",
          permission: ["Student"]
        },
        {
          icon: "mdi-account-plus",
          title: "Students",
          to: "/admin/student",
          permission: ["Admin","Teacher"]
        },
        {
          icon: "mdi-account-plus",
          title: "Profile",
          to: "/my-profile",
          permission: ["Teacher", "Student"]
        },
        {
          icon: "mdi-ballot-recount",
          title: "Batches",
          to: "/admin/batch",
          permission: ["Admin"]
        }
      ],
      leftDrawer: true,
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: "AMS"
    }
  },
  methods: {
    logout() {
      this.$auth.logout()
    }
  }
}
</script>
