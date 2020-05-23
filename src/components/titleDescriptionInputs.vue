<template>
  <div>
    <v-text-field
      required
      label="Название теста"
      :rules="[ v => !!v || 'Name is required']"
      v-model="test.title"
      class="ml-10 mr-10"
    ></v-text-field>
    <v-text-field
      required
      label="Описание теста"
      :rules="[ v => !!v || 'Name is required']"
      v-model="test.description"
      class="ml-10 mr-10"
    ></v-text-field>
  </div>
</template>

<script>
    export default {
        name: 'titleDescription',
        props:['test'],
    }

</script>
