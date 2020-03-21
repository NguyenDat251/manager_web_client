<template>
  <v-container fill-height fluid grid-list-xl>
    <v-layout justify-center wrap>
      <v-flex md12>
        <v-btn color="blue white--text" to="project/add">Add a projects</v-btn>
        <v-card text flat>
          <v-card-title>
            <v-text-field
              v-model="search"
              append-icon="search"
              label="Search"
              single-line
              hide-details
            ></v-text-field>
          </v-card-title>
          <div>
            <v-data-table
              :headers="headers"
              :items="items"
              :search="search"
              hide-default-footer
              :page.sync="page"
              :items-per-page="itemsPerPage"
              @page-count="pageCount = $event"
            >
              <!-- <template slot="headers" slot-scope="{ header }"> -->
              <template v-slot:header="{ props: { headers } }">
                <thead>
                  <span class="subheading font-weight-light text--darken-3" v-text="headers.text" />
                </thead>
              </template>
              <!-- <template slot="items" slot-scope="{ item }"> -->
              <template v-slot:body="{ items }">
                <tbody>
                  <tr v-for="item in items" :key="item.id">
                    <td>{{ item.name }}</td>
                    <td>{{ item.description }}</td>
                    <td>
                      <v-icon color="tertiary" @click="showMembers(item)">mdi-view-list</v-icon>
                    </td>
                    <td class="text-xs-right">
                      <v-icon color="tertiary" @click="edit(item)">edit</v-icon>
                    </td>
                    <td>
                      <v-icon color="tertiary" @click="deleteMember(item)">mdi-delete</v-icon>
                    </td>
                  </tr>
                </tbody>
              </template>
            </v-data-table>
            <v-pagination v-model="page" :length="pageCount"></v-pagination>
          </div>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>


<script>
export default {
  data: () => ({
    page: 1,
    pageCount: 0,
    itemsPerPage: 5,
    dialog: false,
    btnLock: true,
    search: "",
    headers: [
      {
        text: "Name",
        value: "name"
      },
      {
        text: "Description",
        value: "description"
      }
    ],

    items: []
  }),
  methods: {
    clickItem: function(id) {
      this.$router.push({ path: "/project/edit/" + id });
    },

    async getListProjects() {
      let $this = this;
      await $this.$axios
        .get("/project")
        .then(async function(response) {
          //if(response.data.returnCode == 1){
          // console.log("this members: " +  JSON.stringify(response.data.data))
          await ($this.items = response.data);
          console.log("this project: " + JSON.stringify($this.items));

          // }
          // else{
          //   console.log("this error message: " +  response.data.returnMessage)
          // }
        })
        .catch(function(error) {
          console.log("Error get list project:");
          console.log(error);
        });
    },
    edit(project) {
      this.$router.push({ path: "/project/edit/" + project.id });
    },
    deleteMember(project) {
      let $this = this;
      this.$axios
        .delete("/project/" + project.id)
        .then(async function(response) {
          console.log(response.data);
          console.log($this.items);
          const index = $this.items.indexOf(project);
          console.log("index delete item: " + index);
          $this.items.splice(index, 1);
        })
        .catch(function(error) {
          console.log("Error delete project:");
          console.log(error);
        });
    }
  },
  created: async function() {
    this.getListProjects();
    //console.log()
  }
};
</script>