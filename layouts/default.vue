<template>
  <v-app light>
    
    <v-navigation-drawer app>
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title>
            <div >
              <v-img
                :src="require('@/static/v.png')"
                max-height="100"
                class="mb-2"
              />
            </div>
          </v-list-item-title>
        </v-list-item-content>
      </v-list-item>
      <v-divider></v-divider>
      <v-list>
        
        <v-list-item to="/member">
          <v-list-item-content>
            <v-list-item-title>Members</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        
        <v-list-item to="/project">
          <v-list-item-content>
            <v-list-item-title>Projects</v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        
      </v-list>
    </v-navigation-drawer>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <v-footer></v-footer>
  </v-app>
</template>

<script>

</script>
