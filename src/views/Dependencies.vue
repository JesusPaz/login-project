<template>
  <v-app>
    <div id="Dependencies">
      <br />
      <br />
      <v-form ref="form" v-model="valid">
        <h3>Create a new dependency</h3>
        <v-text-field label="Name" v-model="newDependency.name" :rules="nameRules"></v-text-field>
        <v-text-field label="Coordinator" v-model="newDependency.coordinator" :rules="cordRules"></v-text-field>
        <v-text-field
          label="Max Number of Users"
          v-model="newDependency.maxnumber"
          :rules="numberRules"
        ></v-text-field>
        <v-text-field label="Location" v-model="newDependency.location" :rules="locRules"></v-text-field>
        <v-select v-model="newDependency.active" :items="ops" label="Enabled"></v-select>

        <v-btn @click="addDependency" :disabled="!valid">Submit</v-btn>
      </v-form>

      <br />
      <br />
      <br />
      <br />

      <div>
        <h3>All dependencies</h3>

        <br />
        <br />

        <v-card>
          <v-card-title>
            <v-text-field v-model="search" label="Search" single-line hide-details></v-text-field>
          </v-card-title>
          <v-data-table :headers="headers" :items="deps" :search="search"></v-data-table>
        </v-card>
      </div>
    </div>
  </v-app>
</template>

<script>
import { db } from "./db";

export default {
  name: "Dependencies",
  data() {
    return {
      valid: false,
      search: "",
      headers: [
        { text: "Name", value: "name" },
        { text: "Coordinator", value: "coordinator" },
        { text: "Max Number", value: "maxnumber" },
        { text: "Location", value: "location" },
        { text: "Enabled", value: "active" }
      ],
      ops: ["True", "False"],
      deps: [],
      newDependency: {
        name: "",
        coordinator: "",
        maxnumber: "",
        location: "",
        active: ""
      },
      nameRules: [
        name => !!name || "Name is required",
        name => name.length > 3 || "Name must be longer than 4 chars"
      ],
      cordRules: [
        coordinator => !!coordinator || "Coordinador is required",
        coordinator =>
          coordinator.length > 3 || "Coordinador must be longer than 4 chars"
      ],
      locRules: [
        location => !!location || "Location is required",
        location =>
          location.length > 3 || "Location must be longer than 4 chars"
      ],
      numberRules: [
        maxnumber => !!maxnumber || "Max Number is required",
        maxnumber =>
          /^([0-9])+$/.test(maxnumber) || "Max Number need to be a integer"
      ]
    };
  },
  mounted() {
    this.getDeps();
    },
  computed: {
    items: function() {
      return this.deps;
    }
  },
  methods: {
    addDependency() {
      //dependenciesRef.push(this.newDependency);

      db.collection("dependencies")
        .add(this.newDependency)
        .then(function(docRef) {
          alert("Dependency added");
        })
        .catch(function(error) {
          alert(error);
        });

      this.newDependency.name = "";
      this.newDependency.coordinator = "";
      this.newDependency.maxnumber = "";
      this.newDependency.location = "";
      this.newDependency.active = "";
    },
    async getDeps() {
      await db
        .collection("dependencies")
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