<template>
  <v-container fill-height fluid grid-list-xl>
    <v-layout justify-center wrap>
      <v-flex md12>
        <v-btn color="blue white--text" to="member/add">Add a members</v-btn>
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
              <template v-slot:header="{ props: { headers } }">
                <thead>
                  <span class="subheading font-weight-light text--darken-3" v-text="headers.text" />
                </thead>
              </template>
              <template v-slot:body="{ items }">
                <tbody>
                  <tr v-for="item in items" :key="item.id">
                    <td>{{ item.name }}</td>
                    <td>{{ item.phone }}</td>
                    <td>{{ item.birthdate }}</td>
                    <td>{{ item.ProjectName }}</td>
                    <td class="text-xs-right">
                      <v-tooltip right v-on="on">
                        <template v-slot:activator="{ on }">
                          <v-icon color="tertiary" @click="edit(item)" v-on="on">edit</v-icon>
                        </template>
                        <span class="white--text">Edit member</span>
                      </v-tooltip>
                    </td>
                    <td>
                      <v-tooltip right v-on="on">
                        <template v-slot:activator="{ on }">
                          <v-icon color="tertiary" @click="deleteMember(item)" v-on="on">mdi-account-remove</v-icon>
                        </template>
                        <span class="white--text">Delete member</span>
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
    page: 1,
    pageCount: 0,
    itemsPerPage: 5,
    dialog: false,
    btnLock: true,
    search: "",
    headers: [
      {
        text: "Namme",
        value: "name"
      },
      {
        text: "Phone",
        value: "phone"
      },
      {
        text: "Birthdate",
        value: "birthdate"
      },
      {
        text: "Project name",
        value: "ProjectName"
      }
    ],

    items: [],
    ChoosenItems: []
  }),
  methods: {
    clickItem: function(id) {
      this.$router.push({ path: "/member/edit/" + id });
    },

    async getListMembers() {
      let $this = this;
      await $this.$axios
        .get("/member")
        .then(async function(response) {
          await ($this.items = response.data);
          $this.items.forEach(element => {
            element.birthdate = element.birthdate.substring(0, 10);
          });
          console.log("this members: " + JSON.stringify($this.items));
        })
        .catch(function(error) {
          console.log("Error get list member:");
          console.log(error);
        });
    },
    edit(member) {
      this.$router.push({ path: "/member/edit/" + member.id });
    },
    deleteMember(member) {
      let $this = this;
      this.$axios
        .delete("/member/" + member.id)
        .then(async function(response) {
          console.log(response.data);
          console.log($this.items);
          const index = $this.items.indexOf(member);
          console.log("index delete item: " + index);
          $this.items.splice(index, 1);
        })
        .catch(function(error) {
          console.log("Error delete member:");
          console.log(error);
        });
    }
  },
  created: async function() {
    this.getListMembers();
  }
};
</script>