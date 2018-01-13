<template lang="pug">
    .datepicker-container
        b-dropdown(variant='link', size='lg', no-caret='')
            template(slot='button-content')
                .col-sm-12
                    input.form-control(:value="selectedDate" ref="input")
            calendar(v-on:dateSelected="onDateSelect")
</template>

<script>
import Calendar from "./Calendar.vue";

export default {
    data: function() {
        return {
            selectedDate: ''
        }
    },
    components: {
        'calendar': Calendar
    },
    methods: {
        onDateSelect: function(date) {
            console.log("onDateSelect:" + date);
            this.selectedDate = date;
            //close the dropdown
            this.$refs.input.click();
        }
    }
}
</script>

<style scoped>
</style>
