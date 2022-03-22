<template>
  <div>
    <h1 class="display-2">Plüss állatok</h1>
    <table class="table">
      <thead>
        <tr>
          <th>Azonosító</th>
          <th>Név</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="toy in toys" :key="toy.id">
          <td>{{ toy.id }}</td>
          <td>{{ toy.name }}</td>
          <td>
            <button class="btn btn-primary" @click="startEdit(toy)">Szerkesztés</button>
            <button class="btn btn-danger" @click="deleteToy(toy.id)">Törlés</button>
          </td>
        </tr>
        <tr>
          <td colspan="2"><input type="text" v-model="newToy.name"></td>
          <td>
            <button class="btn btn-primary" v-if="!editing" @click="postToy">Új plüss</button>
            <button class="btn btn-success" v-if="editing" @click="editToy">Mentés</button>
            <button class="btn btn-danger" v-if="editing" @click="cancelEdit">Mégse</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      editing: false,
      toys: [],
      newToy: {
        id: 0,
        name: ""
      }
    } 
  },
  methods: {
    async getToys() {
      fetch('http://localhost:8000/api/toys')
        .then( response => response.json() )
        .then( response => this.toys = response)
      
      //console.log(this.toys)
    },
    async postToy() {
      await fetch('http://localhost:8000/api/toys', {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          "Accept": "application/json",
        },
        body: JSON.stringify(this.newToy)
      })

      await this.getToys()
    },
    async deleteToy(id) {
      await fetch(`http://localhost:8000/api/toys/${id}`, {
        method: "DELETE"
      })

      await this.getToys()
    },
    async editToy() {
      await fetch(`http://localhost:8000/api/toys/${this.newToy.id}`, {
        method: "PATCH",
        headers: {
          "Content-Type": "application/json",
          "Accept": "application/json",
        },
        body: JSON.stringify(this.newToy)
      })

      await this.getToys()
      this.cancelEdit()
    },
    startEdit(toy) {
      this.editing = true

      this.newToy.id = toy.id
      this.newToy.name = toy.name
    },
    cancelEdit() {
      this.editing = false

      this.newToy.id = 0
      this.newToy.name = ""
    }
  },
  mounted() {
    this.getToys()
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
