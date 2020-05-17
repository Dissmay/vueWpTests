<template>
<v-app dark>
<v-content>
  <v-layout row> 
    <v-flex xs12 sm6 offset-sm3  align="center" class="">
        <v-form ref="form" v-model="valid">
          <v-text-field
            v-model="question"
            label="question"
            :rules="[ v => !!v || 'Name is required']"
            required
          ></v-text-field>
          <v-layout row v-for="key in vars" :key="key.id">
            <v-text-field
              required
              label="Введите вопрос"
              :rules="[ v => !!v || 'Name is required']"
              v-model="key.title"
            ></v-text-field>
            <v-checkbox 
              v-on:click.once="checkTrue(key.id)" 
              class="mr-10"  
              :key="key.id" 
              :disabled="key.correctAnswer"  
              v-model="key.correctAnswer"></v-checkbox>
          </v-layout>
          <v-btn
            :disabled="!valid"
            color="success"
            class="mr-4"
            @click="createTest()"
          >
            Создать тест
          </v-btn>
          <v-btn @click="addQuestion">
              Добавить вопрос
          </v-btn>
        </v-form>
    </v-flex>
  </v-layout>
    <div>
      <v-text-field
        required
        label="Введите вопрос"
        :rules="[ v => !!v || 'Name is required']"
        v-model="test.title"
        class="ml-10 mr-10"
      ></v-text-field>
       <v-text-field
        required
        label="Введите вопрос"
        :rules="[ v => !!v || 'Name is required']"
        v-model="test.description"
        class="ml-10 mr-10"
      ></v-text-field>
    </div>
  <v-layout>
    <v-layout row>
      <v-flex xs12 sm6 offset-sm3 mt-10  class="d-flex justify-space-between mb-6 flex-wrap">
        <v-card
            class="mx-auto"
            max-width="344"
            outlined
            v-for="question in test.questions"
            :key="question.id"
          >
          <v-list-item three-line>
            <v-list-item-content>
                <div class="overline mb-4">{{question.title}}</div>
                <v-list-item-title 
                  class="headline mb-1 " 
                  v-for="oneVar in question.vars" 
                  v-bind:key="oneVar.id"
                  > 
                   <v-row justify="space-around">
                      <p class="ma-2">{{oneVar.title}}</p>
                    <v-switch v-model="oneVar.correctAnswer" class="ma-2" ></v-switch>
                  </v-row>
                </v-list-item-title>
                <v-list-item-subtitle v-for="correct in correctAnswers(question.id)" :key="correct.title">
                      Правильный ответ:{{correct.title}}
                </v-list-item-subtitle>
                <v-card-actions>
                  <v-row justify="center">
                    <v-btn
                    color="primary"
                    dark
                    @click.stop="editQuestion(question.id)"
                    >
                    Редактировать
                    </v-btn>
                  </v-row>
                </v-card-actions>
            </v-list-item-content>
          </v-list-item>
        </v-card>
      </v-flex>
    </v-layout>
  </v-layout> 
</v-content>
  <v-dialog
    v-model="dialog"
    max-width="800"
    >
    <v-card >
    <v-text-field
      class="ml-5 mr-5"
      :value="modalQuest.title"
      :rules="[ v => !!v || 'Name is required']"
      required
      v-model="modalQuest.title"
    ></v-text-field>
    <v-layout class="ml-10 mr-10" v-for="key in modalQuest.vars" :key="key.id">
      <v-text-field
        required
        label="Введите вопрос"
        :rules="[ v => !!v || 'Name is required']"
        v-model="key.title"
      ></v-text-field>
      <v-btn
        color="green darken-1"
        text
        @click="deleteVarModal(key.id)"
      >
      Удалить инпут
      </v-btn>
    </v-layout>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn
      color="green darken-1"
      text
      @click="addInputModal()"
      >
      Добавить инпут
      </v-btn>

      <v-btn
      color="green darken-1"
      text
      @click="dialog = false"
      >
      Сохранить вопрос
      </v-btn>
    </v-card-actions>
    </v-card>
  </v-dialog>
</v-app>

</template>

<script>
import { v4 as uuidv4 } from 'uuid';
export default {
  name: 'App',
  data: () => ({
    valid: true,
    question: 'this это:',
    vars: [],
    dialog: false,
    modalQuest:{},
    test:{
      title: 'Тесты к уроку 5',
      description: 'Ответьте на все вопросы, и тд и тп',
      questions: []
    },
  }),
  methods: {
    createTest(){
      if(this.$refs.form.validate()){
          this.test.questions.push({
            title: this.question,
            vars: this.vars,
            id: uuidv4()
          })
          this.questions =  ''
          this.vars =  []
      }
    },
    addQuestion(){
      this.vars.push({title:'', id:uuidv4(), correctAnswer: false})
    },
    checkTrue(id){
      this.vars.map(itm => {
        if(itm.id === id){
          itm.correctAnswer = true
        }
      })
    }, 
    correctAnswers(id){
      let returnCorrectMass
      let filterQuest = this.test.questions.filter(e=> e.id == id)
      let newVar = filterQuest[0].vars
      returnCorrectMass = newVar.filter((item) => item.correctAnswer)
      return returnCorrectMass
    },
    editQuestion(id){
      this.dialog = true
      let filterQuest = this.test.questions.filter(e => e.id == id)
      this.modalQuest = filterQuest[0]
    },
    deleteVarModal(id){
      this.modalQuest.vars = this.modalQuest.vars.filter(e=> e.id !== id)
    },
    addInputModal(){
        this.modalQuest.vars.push({title:'', id: uuidv4(), correctAnswer: false})
    }
    },
    computed: {
     
    },
  async created(){
    const receiveJwtToken = {
      'username': 'Dissmay',
      'password': '13ByqWn(%8A#k&9YUy'
    }
    // const json = {
    //     title: 'Тесты к уроку 5',
    //     description: 'Ответьте на все вопросы, и тд и тп',
    //     questions: [
    //         {
    //           text: 'this это:',
    //           type: 'radio',
    //           vars: [ 'слово', 'прикол', 'что за зис?', 'контекст вызова функции' ],
    //           correctAnswer: ['контекст вызова функции']
    //         },
    //       ]
    // }
   let resToken = await  fetch('http://vpvue.use-effect.xyz/wp-json/jwt-auth/v1/token', {
      method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json'
        },
        body: JSON.stringify(receiveJwtToken)
    })
    .then(res => res.json())
    .then(data => {
     return data.token
    })
    console.log(resToken);
    


  
    //  await fetch('http://vpvue.use-effect.xyz/wp-json/add/test/vl',{
    //   method: 'POST',
    //     body:JSON.stringify({json}),
    //     headers: {
    //       'Authorization': 'Bearer '+resToken,
    //       'Content-Type': 'application/json;charset:UTF-8;'
    //     },
    // })
  }
};
</script>
