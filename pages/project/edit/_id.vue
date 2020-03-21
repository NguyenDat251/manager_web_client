<template>
  <v-card>
    <v-card-title>
      <v-btn @click="$router.go(-1)">Back</v-btn>
    </v-card-title>
    <v-card-title class="justify-center">Edit</v-card-title>
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
          @click="edit"
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
    getProject: function(id) {
      this.$axios
        .get("/project/" + id
        )
        .then(response => {
          let thisProject = response.data;
         console.log("this project: " + JSON.stringify(thisProject))
          this.name = thisProject.name;
          this.description = thisProject.description;
        })
        .catch(function(error) {
          //handle error
          console.log("Error:");
          console.log(error);
        });

         
    },
    edit(){
      this.is_loading = true;

        this.$axios
        .put('/project/' + this.id, {
          name: this.name,
          description: this.description
        })
        .then(res => {
          this.$router.go(-1);
          this.is_loading = false;
        })
        .catch(function(error) {
          //handle error
          console.log("Error edit member: ");
          console.log(error);
          this.is_loading = false;
        });
    }
  },
  mounted: async function() {
    this.id = this.$route.params.id
    await this.getProject(this.id)
    //console.log()
  }
  
};
</script>


