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
                            v-on:prevMonth="onPrevMonth"
                            :date="dt1"
                            :prev="daterange"
                            :next="!daterange"
                            )
                    .col-sm-6
                        calendar(
                            v-on:dateSelected="onDate2Select"
                            v-on:nextMonth="onNextMonth"
                            :date="dt2"
                            :daterange="daterange"
                            :prev="!daterange"
                            :next="daterange"
                            )
                .row(v-if="!daterange")
                    .col-sm-12
                        calendar(
                            v-on:dateSelected="onDateSelect"
                            :daterange="daterange"
                            :date="dt"
                            :prev="show"
                            :next="show"
                            )
</template>

<script>
import Calendar from "./Calendar.vue";
import Moment from 'moment';

export default {
    props: {
        daterange: {
            type: Boolean,
            required: true
        }
    },
    data: function() {
        return {
            selectedDate: '',
            dt1: {},
            dt2: {},
            dt: {},
            show: true,
        }
    },
    components: {
        'calendar': Calendar
    },
    methods: {
        onDateSelect: function(m) {
            this.selectedDate = Moment(m).format('Y-MM-DD');
            //close the dropdown
            this.$refs.input.click();
        },
        onDate1Select: function(date) {
        },
        onDate2Select: function(date) {
        },
        onPrevMonth: function(dt) {
            this.dt1 = dt;
            //bring back dt2 by a month
            var m = Moment(this.dt2);
            console.log("onPrevMonth: b " + this.dt2.months + "-" + this.dt2.years);
            m.subtract(1, 'month');
            this.dt2 = m.toObject();
            console.log("onPrevMonth: a " + this.dt2.months + "-" + this.dt2.years);
        },
        onNextMonth: function(dt) {
            this.dt2 = dt;
            //advance dt1 by a month
            var m = Moment(this.dt1);
            m.add(1, 'month');
            this.dt1 = m.toObject();
        }
    },
    created: function() {
        if (this.daterange) {
            //two calendars, this and next month
            var m = Moment().clone();
            this.dt1 = m.toObject();
            this.dt2 = m.add(1, 'month').toObject();
            return;
        }

        this.dt = Moment().clone().toObject();
    }
}
</script>

<style scoped>
</style>
