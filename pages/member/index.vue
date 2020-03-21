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
                    <td>{{ item.phone }}</td>
                    <td>{{ item.birthdate }}</td>
                    <td>{{ item.ProjectName }}</td>
                    <!-- <td class="text-xs-right">{{ item.role }}</td> -->
                    <td class="text-xs-right">

                      <v-icon color="tertiary" @click="edit(item)">edit</v-icon>
                      <!-- <v-btn color="success" @click="dialog=true">Chỉnh sửa</v-btn> -->
                      <!-- <v-dialog v-model="item.dialog" width="700">
                        <template v-slot:activator="{ on }">
                           <v-btn color="success" v-on="on">Chỉnh sửa</v-btn>
                          
                        </template>
                        <edit-form
                          title="Chỉnh sửa thông tin member"
                          :fullName="item.fullName"
                          :phoneNumber="item.phoneNumber"
                          :email="item.email"
                          btn="Cập nhập"
                          @OnClickEdit="updateProfile($event, item)"
                        ></edit-form>
                      </v-dialog> -->
                    </td>
                    <td>
                      <v-icon
                        color="tertiary"
                        @click="deleteMember(item)"
                      >mdi-account-remove</v-icon>
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
    ChoosenItems:[]
  }),
  methods: {
    clickItem: function(id) {
      this.$router.push({ path: "/member/edit/" + id });
    },
    
   async getListMembers(){
     let $this=this
      await $this.$axios.get("/member")
        .then(async function(response) {
          //if(response.data.returnCode == 1){
         // console.log("this members: " +  JSON.stringify(response.data.data))
          await ($this.items = response.data);
          $this.items.forEach(element => {
            element.birthdate = element.birthdate.substring(0, 10);
          });
           console.log("this members: " +  JSON.stringify($this.items))
         
          // }
          // else{
          //   console.log("this error message: " +  response.data.returnMessage)
          // }
        })
        .catch(function(error) {
          console.log("Error get list member:");
          console.log(error);
        });
    },
    edit(member){
      this.$router.push({path: "/member/edit/" + member.id})
    },
    deleteMember(member){
       let $this= this;
      this.$axios.delete("/member/" + member.id)
        .then(async function(response) {
         
          console.log(response.data)
          console.log($this.items)
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
    this.getListMembers()
    //console.log()
  }
};
</script>