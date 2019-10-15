<template>
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
      <v-checkbox
        v-model="newDependency.active"
        :label="`Enabled: ${newDependency.active.toString()}`"
      ></v-checkbox>
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
      <v-simple-table>
        <template v-slot:default>
          <thead>
            <tr>
              <th class="text-left">Name</th>
              <th class="text-left">Coordinador</th>
              <th class="text-left">Max Number</th>
              <th class="text-left">Location</th>
              <th class="text-left">Enabled</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(i, idx) in deps" :key="idx">
              <td>{{ i.name }}</td>
              <td>{{ i.coordinator }}</td>
              <td>{{ i.maxnumber }}</td>
              <td>{{ i.location }}</td>
              <td>{{ i.active }}</td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>
    </div>
  </div>
</template>

<script>
import { db } from "./db";

let query = db.collection("dependencies");

export default {
  name: "Dependencies",
  data() {
    return {
      valid: false,
      deps: [],
      newDependency: {
        name: "",
        coordinator: "",
        maxnumber: "",
        location: "",
        active: false
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
    console.info("mounted, deps:", this.deps); // // => at this point, this.users is not yet ready.
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
          console.log("Document written with ID: ", docRef.id);
        })
        .catch(function(error) {
          console.error("Error adding document: ", error);
          alert(error);
        });

      this.newDependency.name = "";
      this.newDependency.coordinator = "";
      this.newDependency.maxnumber = "";
      this.newDependency.location = "";
      this.newDependency.active = false;
      console.log(this.dependenciesDB);
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