<template lang="pug">
    .datepicker-container
        b-dropdown(variant='link', size='lg', no-caret='')
            template(slot='button-content')
                .col-sm-12
                    input.form-control(:value="selectedDate" ref="input")
            .container-fluid
                .row(v-if="daterange")
                    .col-sm-6
                        calendar(
                            v-on:dateSelected="onDate1Select"
                            v-on:nextMonth="onNextMonth"
                            v-on:prevMonth="onPrevMonth"
                            :month="date1Month"
                            :year="date1Year"
                            :prev="daterange"
                            :next="!daterange"
                            )
                    .col-sm-6
                        calendar(
                            v-on:dateSelected="onDate2Select"
                            v-on:nextMonth="onNextMonth"
                            v-on:prevMonth="onPrevMonth"
                            :month="date2Month"
                            :year="date2Year"
                            :daterange="daterange"
                            :prev="!daterange"
                            :next="daterange"
                            )
                .row(v-if="!daterange")
                    .col-sm-12
                        calendar(
                            v-on:dateSelected="onDateSelect"
                            :month="date1Month"
                            :year="date1Year"
                            :daterange="daterange"
                            :prev="show"
                            :next="show"
                            )
</template>

<script>
import Calendar from "./Calendar.vue";
import Moment from 'moment';

export default {
    data: function() {
        return {
            selectedDate: '',
            date1Month: '',
            date1Year: '',
            date2Month: '',
            date2Year: '',
            daterange: true,
            show: true,
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
        },
        onDate1Select: function(date) {
            console.log("onDateSelect:" + date);
            this.selectedDate = date;
            //close the dropdown
            this.$refs.input.click();
        },
        onDate2Select: function(date) {
            console.log("onDateSelect:" + date);
            this.selectedDate = date;
            //close the dropdown
            this.$refs.input.click();
        },
        onPrevMonth: function() {
            console.log('prevMonth');
            this.date2Month = "Mar"; 
            this.date2Year = "2019"; 
        },
        onNextMonth: function() {
            console.log('nextMonth');
            this.date1Month = "Jan"; 
            this.date1Year = "2019"; 
        }
    },
    created: function() {
        //two calendars, this and next month
        var m = Moment().clone();
        this.date1Month = m.format('MMM');
        this.date1Year = m.format('Y');
        m = m.add(1, "month");
        this.date2Month = m.format('MMM');
        this.date2Year = m.format('Y');
    }
}
</script>

<style scoped>
</style>
