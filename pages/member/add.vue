<template>
  <v-card>
    <v-card-title>
      <v-btn @click="$router.go(-1)">Back</v-btn>
    </v-card-title>
    <v-card-title class="justify-center">Add a member</v-card-title>
    <v-container>
      <v-form ref="form" v-model="valid">
        <v-text-field
          outlined
          label="Name"
          v-model="name"
          :rules="rules.name"
          :required="true"
          dense
        ></v-text-field>

        <v-text-field
          outlined
          label="Phone"
          v-model="name"
          :rules="rules.name"
          :required="true"
          dense
        ></v-text-field>

        

        <v-select
          :items="roles"
          item-text="display_name"
          item-value="id"
          label="Roles"
          v-model="role_id"
          :rules="rules.role"
          :required="true"
        ></v-select>
        <v-textarea label="Description" outlined v-model="description"></v-textarea>

        <v-btn
          color="primary"
          class="mr-4"
          :loading="is_loading"
          :disabled="!valid"
          @click="addUser"
        >Submit</v-btn>
      </v-form>
    </v-container>
  </v-card>
</template>

<script>
export default {
  data() {
    return {
      valid: true,
      username: "",
      password: "",
      lastname: "",
      firstname: "",
      telephone: "",
      role_id: null,
      roles: [],
      description: "",
      is_loading: false,
      rules: {
        name: [val => (val || "").length > 0 || "This field is required"],
        role: [val => val != null || "This field is required"]
      }
    };
  },
  methods: {
    addUser: function(e) {
      let $this = this;
      this.is_loading = true;
      this.$axios
        .post(this.$api("api.routes.user.create"), {
          username: this.username,
          password: this.password,
          lastname: this.lastname,
          firstname: this.firstname,
          telephone: this.telephone,
          role_id: this.role_id,
          description: this.description
        })
        .then(res => {
          console.log("Response");
          console.log(res.data);
          $this.$router.go(-1);
          $this.is_loading = false;
        })
        .catch(function(error) {
          //handle error
          console.log("Error: ");
          console.log(error.response.data);
          $this.is_loading = false;
        });

      e.preventDefault();
    }
  },
  mounted: function() {
    let $this = this;
    this.$axios
      .get(this.$api("api.routes.user_role.roles.list"))
      .then(response => {
        $this.roles = response.data.roles;
      })
      .catch(function(error) {
        //handle error
        console.log("Error: ");
        console.log(error.response.data);
      });
  }
};
</script>


