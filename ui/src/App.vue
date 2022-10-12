<template>
  <div id="app" class="container-fluid text-center">
    <h1 class="text-info">{{ title }}</h1>
    <p>{{ msg }}</p>

    <div class="col">
      <div v-if="error" class="alert alert-danger" @click="error = !error">
        <strong>Error:</strong> Please add the User name first!
      </div>
      <form @submit.prevent="addUser">
        <div class="input-group mb-3">
          <input type="text" class="form-control" placeholder="User Name" v-model="Email">
          <div class="input-group-append">
            <button v-if="update" class="btn btn-warning" type="submit">
              <i class="fa fa-pencil" aria-hidden="true"></i>
            </button>
            <button v-else class="btn btn-success" type="submit">
              <i class="fa fa-plus" aria-hidden="true"></i>
            </button>
          </div>
        </div>
      </form>

      <button v-if="deleteMultiple" class="btn btn-danger mb-3" @click="deleteMulti">Delete Selected</button>

      <ul class="list-group">
        <li v-for="(User_name,index) in users" :key="index" class="list-group-item list-group-item-info">
          <div class="row">
            <div class="col" @click="loadData(index)">{{ User_name['User'] }}</div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {

    return {
      title: 'user management App',
      msg: 'Add user \'s',
      Email: "",
      users: [

      ],
      error: false,
      update: false,
      updateIndex: null,
      ids: [],
      deleteMultiple: false
    }
  },
  mounted() {
    this.$http.get('http://localhost:8081/api/users').then(function(response) {
      console.log(response)
      this.users = response.data.items ? response.data.items : []
    })
  },
  // This is run whenever the page is loaded to make sure we have a current User list
  created: function() {
    // Use the vue-resource $http client to fetch data from the /users route
    this.$http.get('/user').then(function(response) {
      this.users = response.data.items ? response.data.items : []
    })
  },

  methods: {
    createUser: function() {
      if (!$.trim(this.newUser.name)) {
        this.newUser = {}
        return
      }

      // Post the new User to the /users route using the $http client
      this.$http.put('/users', this.newUser).success(function(response) {
        this.newUser.id = response.created
        this.users.push(this.newUser)
        console.log("User created!")
        console.log(this.newUser)
        this.newUser = {}
      }).error(function(error) {
        console.log(error)
      });
    },

    deleteUser: function(index) {
      // Use the $http client to delete a User by its id
      this.$http.delete('/users/' + this.users[index].id).success(function(response) {
        this.users.splice(index, 1)
        console.log("User deleted!")
      }).error(function(error) {
        console.log(error)
      })
    }
  }
}
</script>

<style>
</style>
