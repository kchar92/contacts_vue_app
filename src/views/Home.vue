<template>
  <div class="home">
    <h1>{{ message }}</h1>

    <p>First name: <input type="text" v-model="contactFirstName"></p>
    <p>Email: <input type="text" v-model="contactEmail"></p>
    <p>Phone Number: <input type="text" v-model="contactPhoneNumber"></p>
    <p>Bio: <input type="text" v-model="contactBio"></p>
    <button v-on:click="createContact()">Create contact</button>
    <hr>

    <div v-for="contact in contacts">
      <p>{{ contact.first_name }}</p>

      <button v-on:click="showInfo(contact)">Show more contact info</button>
      

      

      <div v-if="currentContact === contact">
        <p>{{ contact.phone_number }}</p>
        <p>{{ contact.email }}</p>
        <p>{{ contact.bio }}</p>

        <p>First name: <input type="text" v-model="contact.first_name"></p>
        <p>Email: <input type="text" v-model="contact.email"></p>
        <p>Phone Number: <input type="text" v-model="contact.phone_number"></p>
        <p>Bio: <input type="text" v-model="contact.bio"></p>
        <button v-on:click="updateContact(contact)">Update contact</button>
        <button v-on:click="destroyContact(contact)">Destroy contact</button>      
      </div>

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
      contactPhoneNumber: "",
      contactBio: "",
      currentContact: {}
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
        phone_number: this.contactPhoneNumber,
        bio: this.contactBio
      };

      axios.post('/api/contacts', params).then(response => {
        console.log(response.data);
        this.contacts.push(response.data);
        this.contactFirstName = "";
        this.contactEmail = "";
        this.contactPhoneNumber = "";
      });
    },
    showInfo: function(theContact) {
      console.log(theContact);
      if (this.currentContact === theContact) {
        this.currentContact = null;
      } else {
        this.currentContact = theContact;
      }
    },
    updateContact: function(theContact) {
      console.log('updating the contact');

      var params = {
        first_name: theContact.first_name,
        email: theContact.email,
        phone_number: theContact.phone_number,
        bio: theContact.bio
      };

      axios.patch(`/api/contacts/${theContact.id}`, params).then(response => {
        console.log(response.data);
        theContact.first_name = response.data.first_name;
        theContact.email = response.data.email;
        theContact.phone_number = response.data.phone_number;
        theContact.bio = response.data.bio;
      });
    },
    destroyContact: function(theContact) {
      console.log(theContact);

      axios.delete(`/api/contacts/${theContact.id}`).then(response => {
        
        var index = this.contacts.indexOf(theContact);
        console.log(index);
        this.contacts.splice(index, 1);
      });
    }
  }
};
</script>
