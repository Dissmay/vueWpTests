<template>
    <v-container class=" lighten-5">
      <v-row >
          <v-col 
          cols="4" 
          dark  
          v-for="test in allTests"
          :key="test.id">
            <v-card 
              class="mb-3 mt-5 d-flex justify-space-between mb-4 flex-wrap" 
            >
            <v-card-text>
                <h1 class="ma-1 text-md-center blue-grey--text headline"> 
                  <span class="float-left indigo--text">Название теста:</span>{{test.title}}
                </h1>
                <p class="title ma-1 blue-grey--text text-md-center">
                  <span class="float-left indigo--text">Описание теста:</span>{{test.description}}
                </p>  
                <hr>
              <div class="title  ma-4" v-for="question in test.questions" :key="question.id">
                <p class="ma-2" >
                  <span class="">Вопрос  </span>{{question.title}}
                </p>
                <ul>
                  <li v-for="oneVar in question.vars" :key="oneVar.id" class="ml-5">
                    <p>
                      <v-icon  v-if="oneVar.correctAnswer" dark left class="primary--text">mdi-checkbox-marked-circle</v-icon>
                      {{oneVar.title}} 
                    </p>
                  </li>
                </ul>
              </div>
              <v-layout class="d-flex mt-5 mr-2">
                <modalEditTest :test="test" :token="token"></modalEditTest>
                <modalDelete :id="test.postId" v-on:delete="deleteTest"></modalDelete>
              </v-layout>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
</template>

<script>
  import modalEditTest from './modals/modalEditTest'
  import modalDelete from './modals/modalDelete'
  export default {
    name: 'allTests',
    props:['token'],
    components:{
      modalEditTest,
      modalDelete
    },
    data(){
      return {
        allTests: []
      }
    },
    methods:{
     async deleteTest(id){
       try{
        let result = await fetch(`http://vpvue.use-effect.xyz/wp-json/add/all_tests/test/${id}`,{
          method: 'DELETE', 
          headers: {
          'Authorization': 'Bearer '+this.token,
          'Content-Type': 'application/json;charset=UTF-8;',
          },
          body: JSON.stringify(id)
        })
        if(result){
          this.allTests = this.allTests.filter(test => test.postId !== id)
        }
       }catch(e){
         console.log(e);
       }
     }
    },
    async created(){
      try{
        fetch('http://vpvue.use-effect.xyz/wp-json/add/all_tests/vl',{
        method: 'GET',
        })
        .then(res => res.json())
        .then(res => this.allTests = res);
      }catch(e){
          console.log(e);
      }
    }
  }
</script>
