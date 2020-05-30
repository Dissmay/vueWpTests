<template>
    <v-form ref="form" v-model="validModal">
        {{validModal}}
        <v-card >
        <v-text-field
            class="ml-5 mr-5"
            :value="modalQuest.title"
            :rules="[ v => !!v || 'Name is required']"
            label="Вопрос"
            required
            v-model="modalQuest.title"
        ></v-text-field>
        <v-layout class="ml-10 mr-10" v-for="key in modalQuest.vars" :key="key.id">
            <v-text-field
            required
            class="ma-2"
            label="Вариант ответа"
            :rules="[ v => !!v || 'Name is required']"
            v-model="key.title"
            ></v-text-field>
            <v-switch v-model="key.correctAnswer"></v-switch>
            <v-btn 
            class="mx-2" 
            fab dark 
            color="indigo"
            @click="deleteVarModal(key.id)"
            >
            <v-icon dark>mdi-delete</v-icon>
            </v-btn>
        </v-layout>
        <v-card-actions > 
            <v-spacer></v-spacer>
            <v-btn 
            class="mx-2" 
            fab dark 
            color="indigo"
            @click="addInputModal()"
            >
            <v-icon dark>mdi-plus</v-icon>
            </v-btn>
            <v-btn
            color="primary"
            class="mr-10"
            @click="validModals(modalQuest.vars)"
            :disabled="!validModal"
            >
            {{modalQuestion}}
            </v-btn>
        </v-card-actions>
        </v-card>
    </v-form> 
</template>

<script>
    export default {
        name: 'formEdit',
        props:['modalQuest', 'modalQuestion'],
        data(){
            return{
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
