<template>
  <v-dialog
      v-model="dialog"
      persistent
       hide-overlay
      >
      <formEdit 
      :modalQuest="modalQuest" 
      :validModal="validModal"
      :modalQuestion="modalQuestion"
      v-on:deleteVarModal="deleteVarModal"
      v-on:addInputModal="addInputModal"
      v-on:validModals="validModals"
      ></formEdit>
    </v-dialog>
</template>

<script>
  import formEdit from '../FormEditTests/formEditQuestion'
  export default {
    name: 'modal',
    props:['dialog','modalQuest','modalQuestion'],
    components:{
      formEdit
    },
    data(){
      return {
        validModal:true
      }
    },
    methods:{
      deleteVarModal(id){
          this.$emit('deleteVarModal', id)
      },
      addInputModal(){
            this.$emit('addInputModal')
      },
      validModals: function(args){
          this.$emit('validModals', args)
      }
    }
  }
</script>
