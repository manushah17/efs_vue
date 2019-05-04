<template>
  <v-container grid-list-md>
    <v-layout row wrap align-left justify-left fill-height>
      <v-flex xs12 sm8 lg7 md5>
         <v-layout column align-center>
       <v-flex xs6 sm8 md7>

         <v-alert v-if="showMsg === 'error'"
                dismissible
        :value="true"
        type="error"
      >
            Please verify Customer information.
      </v-alert>
       </v-flex>
         </v-layout>
        <v-card class="login-card">
          <v-card-title>
            <span class="headline">{{pageTitle}}</span>
          </v-card-title>

          <v-spacer/>

          <v-card-text>


            <v-form ref="form" lazy-validation>
              <v-container>

                <v-text-field
                  v-model="customer.cust_number"
                  label="Customer Number"
                  required
                  type="number"
                />

                <v-text-field
                  v-model="customer.name"
                  label="Name"
                  required
                />
                <v-text-field
                  v-model="customer.address"
                  label="Address"
                  required
                />
                <v-text-field
                  v-model="customer.city"
                  label="City"
                  required
                />
                <v-text-field
                  v-model="customer.state"
                  label="State"
                  required
                />
                <v-text-field
                  v-model="customer.zipcode"
                  label="ZipCode"
                  required
                />
                <v-text-field
                  v-model="customer.email"
                  label="Email"
                  required
                />
                <v-text-field
                  v-model="customer.cell_phone"
                  label="Phone"
                  required
                />

              </v-container>
              <v-btn v-if="!isUpdate" class="blue white--text" @click="createProduct">Save</v-btn>
              <v-btn v-if="isUpdate" class="blue white--text" @click="updateProduct">Update</v-btn>
              <v-btn class="white black--text" @click="cancelOperation">Cancel</v-btn>


            </v-form>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>


<script>
  import axios from 'axios';
  import swal from 'sweetalert2';
  import router from '../router';
  const API_URL = `http://localhost:8000`;

  export default {
    name: 'CustomerCreate',
    components: {},
    data() {
      return {
        showError: false,
        customer: {},
        pageTitle: "Add New Customer",
        isUpdate: false,
        showMsg: '',
      };
    },
    methods: {
      createProduct() {
        console.log('create product before call' + JSON.stringify(this.customer));
        const url = `${API_URL}/api/customers/`;
        let jwtToken = localStorage.getItem('token');
        console.log("AuthService.getAuthToken() --" + localStorage.getItem('token'));
        axios.post(url, this.customer, {headers: {Authorization: `jwt ${jwtToken}`}}).then(response => {
          console.log(response);
          // success
          console.log('response.status--' + response.status);
          if (response.status === 201) {
            this.customer = response.data;
           // router.push("/customer-list");
            this.showMsg = "";
            router.push('/customer-list/new');
          }else{
              this.showMsg = "error";
          }
        }).catch(error => {
          console.log('error.response.status--' + error.response.status);
          if (error.response.status === 401) {
            router.push("/auth");
          }else if (error.response.status === 400) {
            this.showMsg = "error";
          }
        });
      },
      cancelOperation(){
         router.push("/customer-list");
      },
      updateProduct() {
        console.log('create product before' + JSON.stringify(this.customer));
        const url = `${API_URL}/api/customers/${this.customer.pk}`;
        let jwtToken = localStorage.getItem('token');
        console.log("AuthService.getAuthToken() --" + localStorage.getItem('token'));

        axios.put(url, this.customer, {headers: {Authorization: `jwt ${jwtToken}`}}).then(response => {
          console.log(response);
          // success
          console.log('response.status--' + response.status);
          if (response.status === 200) {
            this.customer = response.data;
           // router.push("/customer-list");
            router.push('/customer-list/update');
          }else{
              this.showMsg = "error";
          }
        }).catch(error => {
          console.log('error.response.status--' + error.response.status);
          if (error.response.status === 401) {
            router.push("/auth");
          }else if (error.response.status === 400) {
            this.showMsg = "error";
          }
        });
      }
    },
    mounted() {
      console.log("update pk --" + this.$route.params.pk);
      if (this.$route.params.pk) {
        this.pageTitle = "Edit Customer";
        this.isUpdate = true;
        console.log(this.$route.params.pk)
        const url = `${API_URL}/api/customers/${this.$route.params.pk}`;
        let jwtToken = localStorage.getItem('token');
        axios.get(url, {headers: {Authorization: `jwt ${jwtToken}`}}).then(response => {
          console.log("get customer in response --" + response);
          this.customer = response.data;
        }).catch(error => {
          console.log('error.response.status--' + error.response.status);
          if (error.response.status === 401) {
            router.push("/auth");
          }
        });
      }
    },
  }
</script>
<style scoped>
  .aform {
    margin-left: auto;
    width: 60%;
  }
</style>
