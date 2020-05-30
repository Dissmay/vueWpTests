<template>
    <v-alert v-if="msgSendTest" type="success">
      {{msgSendTest}}
    </v-alert>
</template>

<script>
    export default {
        name: 'alert',
        props:['msgSendTest'],

    }
</script>
