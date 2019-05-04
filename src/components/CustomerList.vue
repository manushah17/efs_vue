<template>
  <main>
    <br/>
    <v-container fluid grid-list-md>
      <v-layout column align-left>
        <blockquote>
         Welcome {{validUserName}}!
          <footer>
            <small>
              <em>&mdash;Eagle Financial Services, your Midwest Financial Services Partner.</em>
            </small>
          </footer>
        </blockquote>
      </v-layout>

       <v-layout column align-center>
       <v-flex xs6 sm8 md7>
       <v-alert v-if="showMsg === 'new'"
                dismissible
        :value="true"
        type="success"
      >
        New customer has been added.
      </v-alert>
      <v-alert v-if="showMsg === 'update'" dismissible
        :value="true"
        type="success"
      >
        Customer information has been updated.
      </v-alert>
         <v-alert v-if="showMsg === 'deleted'" dismissible
        :value="true"
        type="success"
      >
        Selected Customer has been deleted.
      </v-alert>

       </v-flex>
       </v-layout>
      <br/>
      <v-container fluid grid-list-md fill-height>
      <v-layout column>
        <v-flex md6>
      <v-data-table
        :headers="headers"
        :items="customers"
        hide-actions
        class="elevation-1"
        fixed
        style="max-height: 300px; overflow-y: auto"
      >

        <template slot="items" slot-scope="props" >
          <td>{{ props.item.cust_number }}</td>
          <td>{{ props.item.name }}</td>
          <td nowrap="true">{{ props.item.address }}</td>
          <td nowrap="true">{{ props.item.city }}</td>
          <td nowrap="true">{{ props.item.state }}</td>
          <td nowrap="true">{{ props.item.zipcode }}</td>
          <td nowrap="true">{{ props.item.email }}</td>
          <td nowrap="true">{{ props.item.cell_phone }}</td>
          <td nowrap="true">
            <v-icon @click="updateCustomer(props.item)">edit</v-icon>
          </td>
          <td nowrap="true">
            <v-icon @click="deleteCustomer(props.item)">delete</v-icon>
          </td>

        </template>

      </v-data-table>
        </v-flex>
      </v-layout>
      </v-container>

      <v-btn class="blue white--text" @click="addNewCustomer">Add Customer</v-btn>
    </v-container>

  </main>
</template>


<script>
  import axios from 'axios';
  import swal from 'sweetalert2';
  import router from '../router';

  const API_URL = `http://localhost:8000`;

  export default {
    name: "CustomerList",
    data: () => ({
      customers: [],
      validUserName: "Guest",
      customerSize: 0,
      showMsg: '',
      headers: [
        {
          text: 'Customer Number',
          align: 'left',
          sortable: false,

        },
        {text: 'Name', sortable: false, align: 'left',},
        {text: 'Address', sortable: false, align: 'left',},
        {text: 'City', sortable: false, align: 'left',},
        {text: 'State', sortable: false, align: 'left',},
        {text: 'ZipCode', sortable: false, align: 'left',},
        {text: 'Email', sortable: false, align: 'left',},
        {text: 'Phone', sortable: false, align: 'left',},
        {text: 'Update', sortable: false, align: 'left',},
        {text: 'Delete', sortable: false, align: 'left',}
      ],

    }),
    mounted() {
      this.getCustomers();
      this.showMessages();
    },
    methods: {
      showMessages(){
        console.log(this.$route.params.msg)
         if (this.$route.params.msg) {
           this.showMsg = this.$route.params.msg;
          // this.isUpdate = true;

         }
      },
      getCustomers() {
        const url = `${API_URL}/api/customers/`;
        let jwtToken = localStorage.getItem('token');
        console.log("jwtToken --" + jwtToken);
        axios.get(url, {headers: {Authorization: `jwt ${jwtToken}`}}).then(response => {
          this.customers = response.data.data;
          this.customerSize = this.customers.length;
          if (localStorage.getItem("isAuthenticates")
            && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
            this.validUserName = JSON.parse(localStorage.getItem("log_user"));
          }
          console.log("customers -size-" + this.customers.length + "  string--" + JSON.stringify(this.customers));
        }).catch(error => {
          console.log('error.response.status--' + error.response.status);
          if (error.response.status === 401) {
            localStorage.removeItem('isAuthenticates');
            localStorage.removeItem('log_user');
            localStorage.removeItem('token');
            router.push("/auth");
          }
        });
      },
      addNewCustomer() {
        console.log("add new customer---" + JSON.parse(localStorage.getItem("isAuthenticates")));
        if (localStorage.getItem("isAuthenticates")
          && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
          router.push('/customer-create');
        } else {
          router.push("/auth");
        }
      },
      updateCustomer(customer) {
        console.log("update customeer---" + JSON.stringify(customer));
        router.push('/customer-create/' + customer.pk);
      },
      deleteCustomer(customer) {
        console.log("delete customeer---" + JSON.stringify(customer));
        const url = `${API_URL}/api/customers/${customer.pk}`;
        //const url = `http://localhost:8000/api/customers/${customer.pk}`;
        let jwtToken = localStorage.getItem('token');
        axios.delete(url, {headers: {Authorization: `jwt ${jwtToken}`}}).then(response => {
          if (response.status === 204) {
            alert("Customer deleted");
            this.showMsg = 'deleted';
            this.$router.go();
          }
        }).catch(error => {
          console.log('error.response.status--' + error.response.status);
          if (error.response.status === 401) {
            localStorage.removeItem('isAuthenticates');
            localStorage.removeItem('log_user');
            localStorage.removeItem('token');
            router.push("/auth");
          }
        });
      }
    }
  };
</script>
