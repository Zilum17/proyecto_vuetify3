<template>
  <v-app>
    <v-app-bar
      class="px-3"
      density="compact"
      flat
      elevation="2"
    >
      <v-avatar
        class="hidden-md-and-up"
        color="grey-darken-1"
        size="32"
      ></v-avatar>

      <v-spacer></v-spacer>

      <v-tabs align-tabs="center" color="grey-darken-2">
        <v-tab
          v-for="link in links"
          :key="link.text"
          :to="link.to"
          replace
          exact
          >
          <router-link :to="link.to" class="tab-link">{{ link.text }}</router-link>
        </v-tab>
      </v-tabs>
      
      <v-spacer></v-spacer>

      <v-avatar
        class="hidden-sm-and-down"
        color="grey-darken-1"
        size="32"
      ></v-avatar>
    </v-app-bar>
    <router-view/>
  </v-app>
</template>
<script setup>
  const links = [
    {text: 'Home', to: '/'},
    {text: 'Update', to: '/updates'},
    {text: 'Profile', to: '/profile'},
    {text: 'Contact', to: '/contact'}
  ]
</script>
<style scoped>
  .tab-link {
    text-decoration: none;
    color: inherit;
  }
</style>
