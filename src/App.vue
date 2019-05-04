<template>

  <v-app>
    <v-toolbar class="blue">
      <v-toolbar-title>Eagle Finance Service</v-toolbar-title>
      <v-toolbar-items>
        <v-btn flat dark @click="goHome">Home</v-btn>
        <v-btn flat dark @click="viewCustomers">Customers</v-btn>
         <v-btn flat dark >Stocks</v-btn>
         <v-btn flat dark >Investments</v-btn>
        <v-btn flat dark v-if="!authenticated"
               @click="login">Log in
        </v-btn>
        <v-btn flat dark v-if="authenticated"
               @click="logout">Log Out
        </v-btn>
      </v-toolbar-items>
    </v-toolbar>
    <v-content>

      <router-view/>
    </v-content>
  </v-app>
</template>

<script>
  import router from './router';

  export default {
    name: 'App',
    data: () => ({
      authenticated: false,
    }),

    mounted() {
      this.checkLoggedIn();
    },

    methods: {
      logout() {
        localStorage.removeItem('isAuthenticates');
        localStorage.removeItem('log_user');
        localStorage.removeItem('token');
        this.authenticated = false;
        router.push('/');
      },
      viewCustomers() {
        router.push('/customer-list');
      },
      login() {
        router.push("/auth");
      },
      checkLoggedIn() {
        if (localStorage.getItem("isAuthenticates")
          && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
          this.authenticated = true;
        } else {
          localStorage.removeItem('isAuthenticates');
          localStorage.removeItem('log_user');
          localStorage.removeItem('token');
          this.authenticated = false;
        }
      },
      goHome() {
        router.push('/');
      }
    }
  };
</script>
