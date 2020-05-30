<template>
    <v-row justify="center">
        <v-dialog 
        v-model="dialog" 
        fullscreen 
        hide-overlay 
        persistent
        transition="dialog-bottom-transition"
        >
        <template v-slot:activator="{ on }">
            <v-btn color="primary" dark v-on="on">Редактировать тест</v-btn>
        </template>
            <v-card>
                <v-toolbar dark color="primary">
                    <v-btn icon dark @click="dialog = false">
                        <v-icon>mdi-close</v-icon>
                    </v-btn>
                    <v-toolbar-title>Settings</v-toolbar-title>
                    <v-spacer></v-spacer>
                    <v-toolbar-items>
                        <v-btn dark text @click="saveTest">{{saveBtnText}}</v-btn>
                    </v-toolbar-items>
                </v-toolbar>
                <formEditTest :test="test"></formEditTest>
                <v-btn @click="addQuestion">Добавить вопрос</v-btn>
            </v-card>
        </v-dialog>
    </v-row>
</template>

<script>
    import { v4 as uuidv4 } from 'uuid';
    import formEditTest from '../FormEditTests/formEditTest'
    export default {
        name: 'modalEditTest',
        props:['test','token'],
        components:{
            formEditTest
        },
        data(){
            return{
                dialog: false,
                saveBtnText: 'Сохранить'
            }
        },
        methods:{
            async saveTest(){
                if(this.test.title !== '' && this.test.description !== ''){
                    this.test.questions.forEach(q => {
                        let newCorrectAnswer = [];
                         q.vars.map(v => {
                            if(v.correctAnswer == true){
                                newCorrectAnswer.push(v)
                            }
                        });
                        q.correctAnswer = []
                        q.correctAnswer = newCorrectAnswer
                    })
                    let json = this.test
                    try{
                    fetch(`http://vpvue.use-effect.xyz/wp-json/add/all_tests/test/${this.test.postId}`,{
                    method: 'POST', 
                    headers: {
                    'Authorization': 'Bearer '+this.token,
                    'Content-Type': 'application/json;charset=UTF-8;',
                    },
                    body: JSON.stringify({json})
                    })
                    this.dialog = false
                    }catch(e){
                        console.log(e);
                    }

                }else{
                    this.saveBtnText = 'Нужно ввести название теста либо Описание теста'
                }
            },
            addQuestion(){
                this.test.questions.push(
                    { 
                        title: " ", 
                        vars: [ { title: "", id: uuidv4(), correctAnswer: false }], 
                        id:uuidv4(),
                        correctAnswer:[]
                    }
                )
            }
        }
    }
</script>

