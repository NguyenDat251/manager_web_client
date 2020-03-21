<template>
  <v-card>
    <v-card-title>
      <v-btn @click="$router.go(-1)">Back</v-btn>
    </v-card-title>
    <v-card-title class="justify-center">Add a project</v-card-title>
    <v-container>
      <v-form ref="form" v-model="valid">
        <v-text-field
          outlined
          label="Name"
          v-model="name"
          :rules="[rules.required]"
          dense
        ></v-text-field>

        <v-textarea label="Description" outlined v-model="description"></v-textarea>
        
        <v-btn
          color="primary"
          class="mr-4"
          :loading="is_loading"
          :disabled="!valid"
          @click="addProject"
        >Submit</v-btn>
      </v-form>
    </v-container>
  </v-card>
</template>

<script>
export default {
  data() {
    return {
        name: '',
        description: '',
        modal: false,
      valid: true,
      is_loading: false,
      rules: {
        required: val => !!val ||  "This field is required"
      }
    };
  },
  methods: {
    addProject: function(e) {
      let $this = this;
      this.is_loading = true;
      this.$axios
        .post('/project', {
          name: this.name,
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
  }
};
</script>


