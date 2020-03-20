<template>
  <v-container fill-height fluid grid-list-xl>
    <v-layout justify-center wrap>
      <v-flex md12>
        <v-btn color="green white--text" to="member/add">Add a member</v-btn>
        <material-card text>
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
                    <td class="text-xs-right">{{ item.role }}</td>
                    <td class="text-xs-right">
                      <!-- <v-btn color="success" @click="dialog=true">Chỉnh sửa</v-btn> -->
                      <v-dialog v-model="item.dialog" width="700">
                        <template v-slot:activator="{ on }">
                          <!-- <v-btn color="success" v-on="on">Chỉnh sửa</v-btn> -->
                          <v-icon color="tertiary" v-on="on">edit</v-icon>
                        </template>
                        <edit-form
                          title="Chỉnh sửa thông tin admin"
                          :fullName="item.fullName"
                          :phoneNumber="item.phoneNumber"
                          :email="item.email"
                          btn="Cập nhập"
                          @OnClickEdit="updateProfile($event, item)"
                        ></edit-form>
                      </v-dialog>
                    </td>
                    <td>
                      <v-icon
                        color="tertiary"
                        @click="item.status=!item.status"
                      >{{item.status ? 'lock' : 'lock_open'}}</v-icon>
                    </td>
                  </tr>
                </tbody>
              </template>
            </v-data-table>
            <v-pagination v-model="page" :length="pageCount"></v-pagination>
          </div>
        </material-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>


<script>
export default {
  data() {
    return {
        page: 1,
    pageCount: 0,
    itemsPerPage: 5,
    search: "",
     headers: [
        {
          text: 'Name',
          value: 'name',
        },
        {
          text: 'Phone',
          value: 'phone',
        },
        {
          text: 'Birthdate',
          value: 'birthdate',
        }
      ],
      items: [
        {
          name: 'Dat 1',
          phone: '0343244644',
          birthdate: '30/01/1998'
        },
        {
          name: 'Dat 2',
          phone: '0343244644',
          birthdate: '20/01/1998'
        },
        {
           name: 'Dat 3',
          phone: '0343244644',
          birthdate: '10/01/1998'
        },
        {
           name: 'Dat 4',
          phone: '0343244644',
          birthdate: '11/01/1998'
        },
        {
          name: 'Dat 5',
          phone: '0343244644',
          birthdate: '25/01/1998'
        }
      ],
    };
  },
 
};
</script>