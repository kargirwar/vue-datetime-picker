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
                                :monthYear="mY1"
                                :prev="daterange"
                                :next="!daterange"
                            )
                    .col-sm-6
                        calendar(
                                v-on:dateSelected="onDate2Select"
                                v-on:nextMonth="onNextMonth"
                                :date="dt2"
                                :monthYear="mY2"
                                :prev="!daterange"
                                :next="daterange"
                            )
                .row(v-if="!daterange")
                    .col-sm-12
                        calendar(
                                v-on:dateSelected="onDateSelect"
                                :date="dt"
                                :monthYear="mY"
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
            mY1: {},
            mY2: {},
            mY: {},
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
        onDate1Select: function(dt) {
            //copy to next month
            this.dt2 = dt;
        },
        onDate2Select: function(dt) {
            //copy to prev month
            this.dt1 = dt;
        },
        onPrevMonth: function(mY) {
            this.mY1 = mY;
            console.log("onPrevMonth: " + this.mY1.months + '-' + this.mY1.years);
            var m = Moment(this.mY1);
            m.add(1, 'month');
            this.mY2 = m.toObject();
            console.log("onPrevMonth: " + this.mY2.months + '-' + this.mY2.years);
        },
        onNextMonth: function(mY) {
            this.mY2 = mY
            //advance dt1 by a month
            var m = Moment(this.mY1);
            m.add(1, 'month');
            this.mY1 = m.toObject();
        }
    },
    created: function() {
        if (this.daterange) {
            //two calendars, this and next month
            var m = Moment().clone();
            this.mY1 = m.toObject();
            //this.dt1 = m.toObject();
            this.mY2 = m.add(1, 'month').toObject();
            //this.dt2 = this.mY2;
            return;
        }

        this.mY = Moment().clone().toObject();
    }
}
</script>

<style scoped>
</style>
