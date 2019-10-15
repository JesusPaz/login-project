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
            
            <tr v-for="(i, idx) in dependenciesDB" :key="idx">
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
import { db } from './db';


export default {
  name: "Dependencies",
  // async mounted() {
  //   this.dependenciesDB = await firebase.firestore().collection('dependencies');
  // },
  firebase: {
    dependenciesDB: db.collection('depencencies').orderBy('name'),
  },
  data() {
    return {
      valid: false,
      dependenciesDB: [],
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
  methods: {
    addDependency() {
      dependenciesRef.push(this.newDependency);
      this.newDependency.name = "";
      this.newDependency.coordinator = "";
      this.newDependency.maxnumber = "";
      this.newDependency.location = "";
      this.newDependency.active = false;
      console.log(this.dependenciesDB);
      console.log(this.dependenciesRef);
    }
  }
};
</script>

<style>
</style>