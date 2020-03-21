<template>
  <v-card>
    <v-card-title>
      <v-btn @click="$router.go(-1)">Back</v-btn>
    </v-card-title>
    <v-card-title class="justify-center">Edit</v-card-title>
    <v-container>
      <v-form ref="form" v-model="valid">
        <v-text-field outlined label="Name" v-model="name" :rules="[rules.required]" dense></v-text-field>

        <v-text-field
          outlined
          label="Phone"
          v-model="phone"
          :rules="[rules.required, rules.phone]"
          dense
        ></v-text-field>

        <v-dialog ref="dialog" v-model="modal" :return-value.sync="date" persistent width="290px">
          <template v-slot:activator="{ on }">
            <v-text-field v-model="date" label="Birthdate" prepend-icon="event" readonly v-on="on"></v-text-field>
          </template>
          <v-date-picker v-model="date" scrollable>
            <v-spacer></v-spacer>
            <v-btn text color="primary" @click="modal = false">Cancel</v-btn>
            <v-btn text color="primary" @click="$refs.dialog.save(date)">OK</v-btn>
          </v-date-picker>
        </v-dialog>

        <v-select
          :items="projects"
          item-text="name"
          item-value="id"
          label="Projects"
          v-model="projectId"
        ></v-select>

        <v-btn
          color="primary"
          class="mr-4"
          :loading="is_loading"
          :disabled="!valid"
          @click="edit()"
        >Submit</v-btn>
      </v-form>
    </v-container>
  </v-card>
</template>

<script>
export default {
  data() {
    return {
        id: null,
      date: new Date().toISOString().substr(0, 10),
      name: "",
      phone: "",
      projectId: null,
      projects: [],
      modal: false,
      valid: true,
      is_loading: false,
      rules: {
        //required: false
        required: val => !!val || "This field is required",
        phone: value => {
          const pattern = /(09|01[2|6|8|9]|03)+([0-9]{8})\b/g;
          return pattern.test(value) || "That's not a phone number";
        }
      }
    };
  },
  methods: {
    getMember: function(id) {
        console.log("get member")
      this.$axios
        .get("/member/" + id
        )
        .then(response => {
          let thisMember = response.data;
          console.log("thisMember: " + JSON.stringify(thisMember))
          this.name = thisMember.name;
          this.phone = thisMember.phone;
          this.date = thisMember.birthdate.substr(0,10);
          this.projectId = thisMember.projectID;
        })
        .catch(function(error) {
          //handle error
          console.log("Error:");
          console.log(error);
        });

         this.$axios
      .get("/project")
      .then(response => {
        this.projects = response.data;
      })
      .catch(function(error) {
        //handle error
        console.log("Error get membe: ");
        console.log(error.response.data);
      });
    },
    edit(){
      this.is_loading = true;

        this.$axios
        .put('/member/' + this.id, {
          name: this.name,
          phone: this.phone,
          birthdate: this.date,
          projectId: this.projectId
        })
        .then(res => {
          this.$router.go(-1);
          this.is_loading = false;
        })
        .catch(function(error) {
          console.log("Error edit member: ");
          console.log(error);
          $this.is_loading = false;
        });
    }
  },
  mounted: async function() {
    this.id = this.$route.params.id
    await this.getMember(this.id)
  }
  
};
</script>


