<template>
  <v-flex xs12 style="margin-bottom: 80px">
    <v-toolbar dark color="primary" app height="60">
      <v-toolbar-items>
        <v-btn flat>
          <v-img
            :src="require('../../assets/logo.jpg')"
            max-height="100"
            max-width="100"
          >LOGO IMAGE</v-img>
        </v-btn>
        <slot name="left-part"></slot>
      </v-toolbar-items>
      <v-spacer></v-spacer>
      <v-toolbar-items>
        <slot></slot>
      </v-toolbar-items>
    </v-toolbar>
  </v-flex>
</template>

<script>
export default {
  name: 'HeaderBase',
  data: function () {
    return {
    }
  }
}
</script>

<style scoped>

</style>
