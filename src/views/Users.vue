<template>
  <v-app id="vue-app-color">
    <div id="Users">
      <v-form ref="form" v-model="valid" class="mb-12 mt-12">
        <h3>Crear un usuario</h3>
        <v-text-field label="Nombre" v-model="user.name" :rules="nameRules"></v-text-field>
        <v-text-field label="Apellido" v-model="user.lastname" :rules="lastnameRules"></v-text-field>
        <v-text-field label="Email" v-model="user.email" :rules="emailRules"></v-text-field>
        <v-text-field label="Contraseña" :type="'password'" v-model="user.password"></v-text-field>
        <v-text-field label="Valido hasta" v-model="user.validto"></v-text-field>

        <v-select v-model="user.dependency" :items="namedependencies" label="Dependencies"></v-select>
        <v-select v-model="user.active" :items="ops" label="Activo"></v-select>
        <v-btn :disabled="!valid" @click="submit">Crear usuario</v-btn>
      </v-form>
      <div>
        <h3>Usuarios creados hasta el momento</h3>
        <v-card class="mt-6 mb-12">
          <v-card-title >
            <v-text-field v-model="search" label="Search" single-line hide-details></v-text-field>
          </v-card-title>
          <v-data-table
            :search="search"
            :headers="headers"
            :items="deps"
            sort-by="name"
            class="elevation-1"
          >
            <template v-slot:top></template>
            <template v-slot:item.action="{ item }">
              <v-icon small class="mr-2" @click="editUser(item)">edit</v-icon>
              <v-icon small @click="deleteUser(item)">delete</v-icon>
            </template>
          </v-data-table>
        </v-card>
      </div>
    </div>
  </v-app>
</template>

<script>
import { db } from "./db";

let query = db.collection("users");

export default {
  name: "Users",

  data() {
    return {
      valid: false,
      deps: [],
      namedependencies: [],
      ops: ["True", "False"],

      search: "",
      headers: [
        { text: "Name", value: "name" },
        { text: "Lastname", value: "lastname" },
        { text: "Email", value: "email" },
        { text: "Password", value: "password" },
        { text: "Valid To", value: "validto" },
        { text: "Dependency", value: "dependency" },
        { text: "Active", value: "active" },
        { text: "Actions", value: "action", sortable: false }
      ],

      user: {
        name: "",
        lastname: "",
        email: "",
        password: "",
        validto: "",
        dependency: "",
        active: ""
      },

      nameRules: [
        name => !!name || "User Name is required",
        name => name.length > 4 || "User Name must be longer than 4 chars"
      ],
      lastnameRules: [
        name => !!name || "User Lastname is required",
        name => name.length > 4 || "User Lastname must be longer than 4 chars"
      ],
      passwordRules: [
        password => !!password || "Password is required",
        password =>
          /^([a-zA-Z0-9]{6,14})+$/.test(password) ||
          "The password must have uppercase and a size between 6 and 14 characters"
      ],
      emailRules: [
        email => !!email,
        email =>
          /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,24}))$/.test(
            email
          ) || "Write a correct email"
      ]
    };
  },

  mounted() {
    this.getDeps();
    this.getDepeIds();
  },
  computed: {
    items: function() {
      return this.deps;
    }
  },

  methods: {
    submit() {
      db.collection("users")
        .doc(this.user.email)
        .set(this.user);
      alert("Usuario registrado correctamente");
      this.user.name = "";
      this.user.lastname = "";
      this.user.email = "";
      this.user.password = "";
      this.user.validto = "";
      this.user.dependency = "";
      this.user.active = "";
      console.log(this.users);
    },
    editUser(userParam) {
      this.user.name = userParam.name;
      this.user.lastname = userParam.lastname;
      this.user.email = userParam.email;
      this.user.password = userParam.password;
      this.user.validto = userParam.validto;
      this.user.dependency = userParam.dependency;
      this.user.active = userParam.active;
      console.log(this.users);
    },
    deleteUser(userParam) {
      db.collection("users")
        .doc(userParam.email)
        .delete()
        .then(function() {
          console.log("Document successfully deleted!");
        })
        .catch(function(error) {
          console.error("Error removing document: ", error);
        });
    },
    async getDeps() {
      await db
        .collection("users")
        .get()
        .then(querySnapshot => {
          querySnapshot.docs.forEach(doc => {
            this.deps.push(doc.data());
          });
        });
    },
    async getDepeIds() {
      await db
        .collection("dependencies")
        .get()
        .then(querySnapshot => {
          querySnapshot.docs.forEach(doc => {
            this.namedependencies.push(doc.data().name);
          });
        });
    }
  }
};
</script>

<style>
#vue-app-color {
  background-color: white;
}
</style>