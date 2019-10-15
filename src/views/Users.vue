<template>
  <div id="Users">
    <v-form ref="form" v-model="valid">
      <h3>Crear un usuario</h3>
      <v-text-field label="Nombre" v-model="user.name" :rules="nameRules"></v-text-field>
      <v-text-field label="Apellido" v-model="user.lastname" :rules="lastnameRules"></v-text-field>
      <v-text-field label="Email" v-model="user.email" :rules="emailRules"></v-text-field>
      <v-text-field label="Contraseña" :type="'password'" v-model="user.password"></v-text-field>
      <v-text-field label="Valido hasta" v-model="user.validto"></v-text-field>
      <v-text-field label="Dependencia" v-model="user.dependency"></v-text-field>
      <v-text-field label="Activo" v-model="user.active"></v-text-field>
      <v-btn :disabled="!valid" @click="submit">Crear usuario</v-btn>
    </v-form>
    <div>
      <h3>Usuarios creados hasta el momento</h3>

      <v-simple-table>
        <template v-slot:default>
          <thead>
            <tr>
              <th>Nombre</th>
              <th>Apellido</th>
              <th>Email</th>
              <th>Contraseña</th>
              <th>Valido hasta</th>
              <th>Dependencia</th>
              <th>Activo</th>
              <th colspan="2">Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(i, idx) in deps" :key="idx">
              <td>{{ i.name }}</td>
              <td>{{ i.lastname }}</td>
              <td>{{ i.email }}</td>
              <td>{{ i.password }}</td>
              <td>{{ i.validto }}</td>
              <td>{{ i.dependency }}</td>
              <td>{{ i.active }}</td>
              <td>
                <button
                  class="ml-3 bg-red hover:bg-red-dark text-white font-bold py-2 px-4 rounded text-xs"
                  v-on:click="deleteUser(i)"
                >Delete</button>
                <button
                  class="ml-3 bg-red hover:bg-red-dark text-white font-bold py-2 px-4 rounded text-xs"
                  v-on:click="editUser(i)"
                >Edit</button>
              </td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>
    </div>
  </div>
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
    console.info("mounted, deps:", this.deps); // // => at this point, this.users is not yet ready.
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
    }
  }
};
</script>

<style>
</style>