<template>
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
</template>

<script>
    export default {
        name: 'listQuestions',
        props:['test'],
        methods:{
            correctAnswers(id){
                this.$emit('correctAnswers', id)
            },
            editQuestion(id){
                this.$emit('editQuestion', id)
            }
        }

    }
</script>
