<template>
<v-app dark>
  <div class="text-center mt-10 mb-10">
    <v-btn rounded color="alert" dark x-large @click="switchComponents">
      {{allTestsBtnName}}
    </v-btn>
  </div>
  <v-divider></v-divider>
  <v-content v-if="!allTestsIf">
    <alert :msgSendTest="msgSendTest"></alert>
    <titleDescription :test="test"></titleDescription>
    <formQuestion :test='test' v-on:testSendvalid="testSendvalid"></formQuestion>
    <listQuestions :test="test" v-on:correctAnswers="correctAnswers" v-on:editQuestion="editQuestion"></listQuestions>
    <v-btn :disabled="!sendTestValid" color="primary" x-large  class="btnRight mr-10" @click="sendTest">{{error}}
      <v-icon dark right>mdi-checkbox-marked-circle</v-icon>
    </v-btn>
  </v-content> 
  <v-content v-else>
    <allTests :token="token"></allTests>
  </v-content> 
  <modalEditQuestion 
    :dialog="dialog" 
    :modalQuest="modalQuest" 
    :modalQuestion="modalQuestion"
    v-on:deleteVarModal="deleteVarModal"
    v-on:addInputModal="addInputModal"
    v-on:validModals="validModals"
  >
  </modalEditQuestion>
</v-app>
</template>

<script>
  import { v4 as uuidv4 } from 'uuid';
  import formQuestion from './components/formQuestion'
  import listQuestions from './components/listQuestions'
  import alert from './components/alerts/alert'
  import modalEditQuestion from './components/modals/modalEditQuestion'
  import titleDescription from './components/titleDescriptionInputs'
  import allTests from './components/allTests'
  export default {
    components:{
      formQuestion,
      alert,
      modalEditQuestion,
      titleDescription,
      allTests,
      listQuestions
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
      allTestsIf:false,
      allTestsBtnName: 'Все тесты',
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
      async sendTest(){
        if(this.test.title !== '' &&  this.test.description !== '' && this.test.questions.length >= 1){
            this.sendTestValid = false;
            let json = this.test;
            json.questions.forEach(q => {
              q.vars.map(e=> {
                if(e.correctAnswer == true){
                q.correctAnswer.push(e)
                }
              })
            })
            let title = this.test.title
            fetch('http://vpvue.use-effect.xyz/wp-json/add/test/vl',{
            method: 'POST',
            body:JSON.stringify({json, title}),
            headers: {
              'Authorization': 'Bearer '+this.token,
              'Content-Type': 'application/json;charset=UTF-8;',
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
      },
      switchComponents(){
        this.allTestsIf = !this.allTestsIf
        if(!this.allTestsIf){
          this.allTestsBtnName = 'Все тесты'
        }else{
         this.allTestsBtnName = 'Создать тест'
        }
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