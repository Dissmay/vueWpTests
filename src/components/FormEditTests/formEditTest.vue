<template>
    <v-container>
        <v-row >
        <v-col cols="12">
            <v-card >
                <v-text-field
                required
                label="Название"
                :rules="[ v => !!v || 'Name is required']"
                v-model="test.title"
                ></v-text-field>
                <v-text-field
                required
                label="Описание"
                :rules="[ v => !!v || 'Name is required']"
                v-model="test.description"
                ></v-text-field>
                <v-row class="ml-10 mr-10 mb-10 pa-md-4 color" v-for="question in test.questions" :key="question.id">
                    <v-col cols="12">
                        <p><span>Вопрос: </span> {{question.title}}</p>
                    </v-col>
                    <div class="border">
                        <p>Варианта ответа на вопрос:</p>
                        <ol>
                            <li class="" large  block v-for="oneVar in question.vars" :key="oneVar.id">
                                <v-icon v-if="oneVar.correctAnswer" 
                                dark left class="primary--text"
                                >mdi-checkbox-marked-circle</v-icon>
                                {{oneVar.title}}
                            </li>
                        </ol>
                    </div>
                    <div class="ma-2">
                        <modalEditReadyQuestion :question="question"></modalEditReadyQuestion>
                    </div>
                    <div class="ma-2">
                        <modalDelete :id="question.id" v-on:delete="deleteQuestion"></modalDelete>
                    </div>
                </v-row>
                    {{test}}
            </v-card>
        </v-col>
    </v-row>
    </v-container>
</template>

<script>
    import { v4 as uuidv4 } from 'uuid';
    import modalEditReadyQuestion from '../modals/modalEditReadyQuestion'
    import modalDelete from '../modals/modalDelete'
    export default {
        name: 'formEditTest',
        props:['test'],
        components:{
            modalEditReadyQuestion,
            modalDelete
        },
        data(){
            return{
                dialog: false
            }
        },
        methods:{
            openModalQuestion(){
                this.dialog = true
            },
            addInputModal(id){
                this.test.questions.forEach(element => {
                    if(element.id == id){
                        element.vars.push({title:'', id: uuidv4(), correctAnswer: false})
                    }
                })
            },
            deleteQuestion(id){
                this.test.questions = this.test.questions.filter(q => q.id !== id)
            }
        }

    }
</script>
<style  scoped>
    .color{
        background-color: #37474F;
    }
    .border{
        padding: 2rem;
        border:3px solid gray;
        border-radius: 10px;
        width: 100%;
    }
</style>