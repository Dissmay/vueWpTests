<template>
  <v-layout row> 
      <v-flex xs12 sm6 offset-sm3  align="center" class="">
        <v-form ref="form" v-model="valid">
          <v-text-field
            v-model="question"
            label="Вопрос"
            :rules="[ v => !!v || 'Name is required']"
            required
          ></v-text-field>
          <v-layout row v-for="key in vars" :key="key.id">
            <v-text-field
              required
              label="Вариант ответа"
              :rules="[ v => !!v || 'Name is required']"
              v-model="key.title"
            ></v-text-field>
            <v-checkbox 
              v-on:click.once="checkTrue(key.id)" 
              class="mr-10"  
              :key="key.id" 
              :disabled="key.correctAnswer"  
              v-model="key.correctAnswer">
            </v-checkbox>
          </v-layout>
          <v-btn
            :disabled="!valid"
            color="success"
            class="mr-4"
            @click="createTest()"
           >
            {{createQuestion}}
          </v-btn>
          <v-btn @click="addQuestion">
              Добавить вариант ответа
          </v-btn>
        </v-form>
      </v-flex>
    </v-layout>
</template>

<script>
import { v4 as uuidv4 } from 'uuid';
  export default {
    name: 'formQuestion',
    props:['test'],
    data: () => ({
      valid: true,
      vars: [],
      question: 'this это:',
      createQuestion: 'Отправить',
    }),
    methods: {
      addQuestion(){
        this.vars.push({title:'', id:uuidv4(), correctAnswer: false})
      },
      createTest(){
        if(this.$refs.form.validate() && this.vars.length >= 2 && this.varsCorrect.length >= 1 ){
            this.test.questions.push({
              title: this.question,
              vars: this.vars,
              id: uuidv4(),
              correctAnswer:[]
            })
            this.questions =  ''
            this.vars =  []
            this.$emit('testSendvalid', true)
            this.createQuestion = 'Отправить'
        }else{
          this.createQuestion = 'Нужно отметить верный результат либо добавить минимум 2 варианта ответа'
        }
      },
      checkTrue(id){
        this.vars.map(itm => {
          if(itm.id === id){
            itm.correctAnswer = true
          }
        })
      }, 
    },
    computed:{
      varsCorrect(){
        return this.vars.filter(e => e.correctAnswer == true)
      }
    }
  }
</script>
