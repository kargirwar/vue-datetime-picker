<template lang="pug">
    .container-fluid
        b-dropdown(variant='link', size='lg', no-caret='')
            template(slot='button-content')
                input.form-control(:value="selectedDate")
            calendar(v-on:dateSelected="onDateSelect")
</template>

<script>
import Calendar from "./Calendar.vue";

export default {
    data: function() {
        return {
            selectedDate: 'mydate'
        }
    },
    components: {
        'calendar': Calendar
    },
    methods: {
        onDateSelect: function(data) {
            console.log("onDateSelect:" + data);
            this.selectedDate = data;
        }
    }
}
</script>

<style>
</style>
