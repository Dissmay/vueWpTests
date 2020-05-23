<template>
<v-app dark>
  <div class="text-center mt-10 mb-10">
    <v-btn rounded color="alert" dark>Все тесты</v-btn>
  </div>
    <v-divider></v-divider>
  <v-content>
    <alert :msgSendTest="msgSendTest"></alert>
    <titleDescription :test="test"></titleDescription>
    <formQuestion :test='test' v-on:testSendvalid="testSendvalid"></formQuestion>
    <v-layout>
      <v-layout row>
        <v-flex xs12 sm6 offset-sm3 mt-10  class="d-flex justify-space-between mb-6 flex-wrap">
          <v-card
              class="mx-auto mb-5"
              min-width="600"
              outlined
              v-for="question in test.questions"
              :key="question.id"
          >
              <v-list-item three-line>
                <v-list-item-content>
                    <div class="overline ma-2 mb-4"><h2>{{question.title}}</h2></div>
                    <v-list-item-title 
                      class="headline mb-1" 
                      v-for="oneVar in question.vars" 
                      v-bind:key="oneVar.id"
                      > 
                      <v-row justify="space-around">
                        <p class="ma-2 pl-5 pr-5" >{{oneVar.title}}</p>
                      </v-row>
                    </v-list-item-title>
                    <v-list-item-subtitle v-for="correct in correctAnswers(question.id)" :key="correct.title">
                      <h2 class="ma-2">
                        <span>Правильный ответ: {{correct.title}}</span> 
                      </h2> 
                    </v-list-item-subtitle>
                    <v-card-actions>
                      <v-row justify="center">
                        <v-btn
                        color="primary"
                        dark
                        text
                        @click.stop="editQuestion(question.id)"
                        >
                        Редактировать
                        <v-icon class="ma-2">mdi-pencil</v-icon>
                        </v-btn>
                      </v-row>
                    </v-card-actions>
                </v-list-item-content>
              </v-list-item>
          </v-card>
        </v-flex>
      </v-layout>
    </v-layout> 
    <v-btn :disabled="!sendTestValid" color="primary"  class="btnRight mr-10" @click="sendTest()">{{error}}
      <v-icon dark right>mdi-checkbox-marked-circle</v-icon>
    </v-btn>
    
  </v-content>  
    <modal 
      :dialog="dialog" 
      :modalQuest="modalQuest" 
      :modalQuestion="modalQuestion"
      v-on:deleteVarModal="deleteVarModal"
      v-on:addInputModal="addInputModal"
      v-on:validModals="validModals"
    >
    </modal>
</v-app>
</template>

<script>
  import { v4 as uuidv4 } from 'uuid';
  import formQuestion from './components/formQuestion'
  import alert from './components/alert'
  import modal from './components/modalEditQustion'
  import titleDescription from './components/titleDescriptionInputs'

  export default {
    components:{
      formQuestion,
      alert,
      modal,
      titleDescription
    },
    name: 'App',
    data: () => ({
      validModal:true,
      sendTestValid: false,
      dialog: false,
      token: null,
      modalQuestion: 'Отправить',
      msgSendTest: false,
      modalQuest:{},
      test:{
        title: '',
        description: '',
        questions: [
          // {
          //   title: '1',
          //   vars:[{title:'', id:uuidv4(), correctAnswer: false}],
          //   id: uuidv4(),
          //   correctAnswer:[]
          // }
        ],
      },
    }),
    methods: {
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
      },
      verify(id){
        this.test.questions.forEach(element => {
          element.vars.forEach(e => {
              if(e.id === id ){
                console.log(true)
              }
          })
        });
      },
      testSendvalid(){
        this.sendTestValid = true
        console.log(this.sendTestValid);
      },
      // addTrueAnswerToMass(onevar, question){
      //     question.correctAnswer.push(onevar)
      // }
      async sendTest(){
        if(this.test.title !== '' &&  this.test.description !== '' && this.test.questions.length >= 1){
            let json = this.test;
            await fetch('http://vpvue.use-effect.xyz/wp-json/add/test/vl',{
            method: 'POST',
            body:JSON.stringify({json}),
            headers: {
              'Authorization': 'Bearer '+this.token,
              'Content-Type': 'application/json;charset:UTF-8;'
            }
            })
            this.modalQuest = {}
            this.test = {
              title: '',
              description: '',
              questions: [
                // {
                //   title: '1',
                //   vars:[{title:'', id:uuidv4(), correctAnswer: false}],
                //   id: uuidv4(),
                //   correctAnswer:[]
                // }
              ],
            }
            this.msgSendTest = 'Тест успешно отправлена!'
            setTimeout(()=>{
                this.msgSendTest = false
            },3000)
            
        }else{
            this.sendTestValid = false
        }
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
    },
    computed :{
    error(){
      let msg;
        if(this.sendTestValid == false){
          msg = 'Не заполнил обязательные поля'
      }
      if(this.sendTestValid == true){
        msg = 'Отправить'
      }
      return msg
    },
    varsCorrect(){
      return this.vars.filter(e => e.correctAnswer == true)
    }
    },
    watch: {
      'test.title' : function(val){
          if(this.test.title !== '' &&  val.length !== '' && this.test.questions.length >= 1){
            this.sendTestValid = true
          }else{
            this.sendTestValid = false
          }
      },
      'test.description' : function(val){
          if(this.test.title !== '' &&  val.length !== '' && this.test.questions.length >= 1){
            this.sendTestValid = true
          }else{
            this.sendTestValid = false
          }
      },
      
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
      this.token = resToken
    
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


<style  scoped>
  .btnRight{
    float: right;
  }
  
</style>