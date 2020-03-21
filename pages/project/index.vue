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
                      <v-dialog ref="dialog" v-model="item.dialog" width="290px">
                        <template v-slot:activator="{ on }">
                          <v-tooltip right v-on="on">
                            <template v-slot:activator="{ on }">
                              <v-icon
                                color="tertiary"
                                @click="showMembers(item)"
                                v-on="on"
                              >mdi-view-list</v-icon>
                            </template>
                            <span class="white--text">Show list members</span>
                          </v-tooltip>
                        </template>
                        <v-data-table :headers="MemberHeaders" :items="members"></v-data-table>
                      </v-dialog>
                    </td>
                    <td class="text-xs-right">
                      <v-tooltip right v-on="on">
                        <template v-slot:activator="{ on }">
                          <v-icon color="tertiary" @click="edit(item)" v-on="on">edit</v-icon>
                        </template>
                        <span class="white--text">Edit project</span>
                      </v-tooltip>
                    </td>
                    <td>
                      
                      <v-tooltip right v-on="on">
                        <template v-slot:activator="{ on }">
                          <v-icon color="tertiary" @click="deleteProject(item)" v-on="on">mdi-delete</v-icon>
                        </template>
                        <span class="white--text">Delete project</span>
                      </v-tooltip>

                      
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
    //modal: false,
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

    items: [],

    MemberHeaders: [
      {
        text: "Name",
        value: "name"
      },
      {
        text: "Phone",
        value: "phone"
      }
    ],
    members: []
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

          $this.items.forEach(Element => {
            Element["dialog"] = false;
          });

          console.log("this project: " + JSON.stringify($this.items));
        })
        .catch(function(error) {
          console.log("Error get list project:");
          console.log(error);
        });
    },
    edit(project) {
      this.$router.push({ path: "/project/edit/" + project.id });
    },
    deleteProject(project) {
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
    },
    async showMembers(item) {
      item.dialog = true;
      let $this = this;
      await $this.$axios
        .get("/project/members/" + item.id)
        .then(async function(response) {
          //if(response.data.returnCode == 1){
          // console.log("this members: " +  JSON.stringify(response.data.data))
          await ($this.members = response.data);
          console.log(
            "this members of project: " + JSON.stringify($this.members)
          );
        })
        .catch(function(error) {
          console.log("Error get list members of project:");
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