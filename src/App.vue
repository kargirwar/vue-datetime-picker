<template lang="pug">
    .container-fluid
        .row
            .col-sm-2
                //TODO:date-time-picker should fill the container. Does not work at this time
                date-time-picker(:daterange="daterange")
</template>

<script>
import DateTimePicker from "./DateTimePicker.vue";

export default {
    data: function() {
        return {
            daterange: true
        }
    },
    components: {
        'date-time-picker': DateTimePicker
    }
}
</script>

<style>
</style>
