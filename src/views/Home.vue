<template>
  <div class="home">
    <h1>{{ message }}</h1>

    <p>First name: <input type="text" v-model="contactFirstName"></p>
    <p>Email: <input type="text" v-model="contactEmail"></p>
    <p>Phone Number: <input type="text" v-model="contactPhoneNumber"></p>
    <button v-on:click="createContact()">Create contact</button>

    <div v-for="contact in contacts">
      <p>{{ contact.first_name }}</p>
      <p>{{ contact.email }}</p>
      <hr>
    </div>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      message: "Contacts!",
      contacts: [],
      contactFirstName: "",
      contactEmail: "",
      contactPhoneNumber: ""
    };
  },
  created: function() {
    console.log('in created');
    axios.get('/api/contacts').then(response => {
      console.log(response.data);
      this.contacts = response.data;
    });
  },
  methods: {
    createContact: function() {
      console.log('creating contact');
      
      var params = {
        first_name: this.contactFirstName,
        email: this.contactEmail,
        phone_number: this.contactPhoneNumber
      };

      axios.post('/api/contacts', params).then(response => {
        console.log(response.data);
        this.contacts.push(response.data);
        this.contactFirstName = "";
        this.contactEmail = "";
        this.contactPhoneNumber = "";
      });
    }
  }
};
</script>
