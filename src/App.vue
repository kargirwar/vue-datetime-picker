<template lang="pug">
    .container-fluid
        .row
            .col-sm-2
                //TODO:date-time-picker should fill the container. Does not work at this time
                date-time-picker(
                    :daterange="daterange"
                    v-on:dateChanged="onDateChange"
                )
</template>

<script>
import DateTimePicker from "./DateTimePicker.vue";

export default {
    data: function() {
        return {
            daterange: true
        }
    },
    methods: {
        onDateChange: function(o) {
            console.log(JSON.stringify(o));
        }
    },
    components: {
        'date-time-picker': DateTimePicker
    }
}
</script>

<style>
</style>
