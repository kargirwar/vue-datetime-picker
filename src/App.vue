<template lang="pug">
    .container-fluid
        .row
            .col-sm-12
                //TODO:date-time-picker should fill the container. Does not work at this time
                date-time-picker
</template>

<script>
import DateTimePicker from "./DateTimePicker.vue";

export default {
    data: function() {
        return {
        }
    },
    components: {
        'date-time-picker': DateTimePicker
    }
}
</script>

<style>
</style>
