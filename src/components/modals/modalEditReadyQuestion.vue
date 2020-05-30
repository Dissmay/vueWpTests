<template>
    <v-row justify="center">
    <v-dialog v-model="dialog" persistent max-width="100%">
      <template v-slot:activator="{ on }">
        <v-btn color="primary" dark v-on="on">Открыть вопрос</v-btn>
      </template>
      <!-- <v-card>
         <v-text-field
                class="ml-5 mr-5"
                :value="question.title"
                :rules="[ v => !!v || 'Name is required']"
                label="Вопрос"
                required
                v-model="question.title"
            ></v-text-field>
        <v-card-text>Let Google help apps determine location. This means sending anonymous location data to Google, even when no apps are running.</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" text @click="dialog = false">Disagree</v-btn>
          <v-btn color="green darken-1" text @click="dialog = false">Agree</v-btn>
        </v-card-actions>
      </v-card> -->
        <formEditQuestion 
        :modalQuest="question" 
        :modalQuestion="modalQuestion"
        v-on:deleteVarModal="deleteVarModal"
        v-on:addInputModal="addInputModal"
        v-on:validModals="validModals"
        ></formEditQuestion>
    </v-dialog>
  </v-row>

</template>

<script>
import { v4 as uuidv4 } from 'uuid';
import formEditQuestion from '../FormEditTests/formEditQuestion'
    export default {
        name: 'modalEditReadyQuestion',
        props:['question'],
        components:{
            formEditQuestion
        },
        data(){
            return{
                dialog:false,
                validModal:true,
                modalQuestion:'Отправить'
            }
        },
        methods:{
             deleteVarModal(id){
                this.question.vars = this.question.vars.filter(e=> e.id !== id)
            },
            addInputModal(){
                this.question.vars.push({title:'', id: uuidv4(), correctAnswer: false})
            },
            validModals(){
                arguments[0].forEach(e => {
                if(e.correctAnswer == true && arguments[0].length >= 2){
                    this.dialog = false
                    this.validModal = true
                    this.modalQuestion = 'Отправить'
                }else{
                    this.modalQuestion = 'Нужно отметить верный результат и добавить минимум два варианта ответа'
                }
                })
            }
        }
    }
</script>
<style  scoped>
    .height{
        height: 100vh;
    }
</style>