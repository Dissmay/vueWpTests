<template>
  <v-row justify="center">
    <v-btn
      color="error"
      dark
      @click="dialog = true"
      class="ml-10"
    >
      Удалить
    </v-btn>
    <v-dialog
      v-model="dialog"
      width="265"
      max-width="290"
    >
      
      <v-card  >
        <v-card-actions class="height"> 
          <v-btn
            color="primary--text "
            @click="dialog = false"
          >
            Отмена
          </v-btn>

          <v-btn
            color="error "
            text
            @click="deleteMethod"
          >
            Удалить
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
  export default {
      props:['id'],
    data () {
      return {
        dialog: false,
      }
    },
    methods:{
        deleteMethod(){
          this.$emit('delete',this.id)
          this.dialog = false
        }
    }
  }
</script>
<style scoped>
    .height{
        min-height:290px;
        height: 290px;
        display: flex;
        justify-content: center;
    }
</style>