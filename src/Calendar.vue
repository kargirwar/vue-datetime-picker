<template lang="pug">
    .calendar-container
        .row
            .col-sm-2(@click="prevMonth")
                i.fas.fa-angle-left(v-if="prev")
            .col-sm-8.text-center {{currMonth}} {{currYear}}
            .col-sm-2(@click="nextMonth")
                i.fas.fa-angle-right(v-if="next")
        .row
            .col-sm-12
                table.table
                    thead
                        tr
                            th(v-for="day in days") {{day}}
                    tbody
                        tr(v-for="week in weeks")
                            td(v-for="d in week" @click="onDateSelect(d['day'])")
                                span(v-bind:class="{today: d['today'], 'other-month': d['other-month']}") {{d['day']}}

</template>

<script>
import bTable from 'bootstrap-vue/es/components/table';
import Moment from 'moment';
const ROWS = 6;
const COLUMNS = 7;

export default {
    props: {
        'month': String,
        'year': String,
        'daterange': Boolean,
        'prev': Boolean,
        'next': Boolean
    },
    data: function() {
        return {
            weeks: [],
            isActive: false,
            currMonth: '',
            currYear: '',
            days: ['Su', 'Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa']
        }
    },
    created() {
        console.log("m: " + this.month + " y: " + this.year);
        var m = Moment().clone();
        m.year(this.year).month(this.month);
        this.fillCalendar(m);
    },
    methods: {
        fillCalendar: function(m) {
            this.currMonth = m.format('MMM');
            this.currYear = m.format('Y');
            this.weeks = [];
            m = m.startOf('month').startOf('week').clone();

            var thisDay = Moment().format('D');
            var thisMonth = Moment().format('M');
            var thisYear = Moment().format('Y');
            var isToday = false;

            var isOtherMonth = false;

            //we want a 7x7 grid starting from the sunday of the week for the 1st of month
            for (var i = 0; i < ROWS; i++) {
                var week = []
                for (var j = 0; j < COLUMNS; j++) {
                    isToday = (
                        thisDay == m.format('D') &&
                        thisMonth == m.format('M') &&
                        thisYear == m.format('Y')) ? true : false;
                    isOtherMonth = (this.currMonth != m.format('MMM')) ? true : false;

                    week.push({
                        'day': m.format('D'),
                        'today': isToday,
                        'other-month': isOtherMonth
                    });
                    m.add(1, 'days');
                }

                this.weeks.push(week);
            }
        },
        nextMonth: function() {
            var m = Moment().clone();
            m.year(this.currYear).month(this.currMonth);
            m.add(1, 'month');
            this.fillCalendar(m);
            this.$emit('nextMonth', this.currMonth);
        },
        prevMonth: function() {
            var m = Moment().clone();
            m.year(this.currYear).month(this.currMonth);
            m.subtract(1, 'month');
            this.fillCalendar(m);
            this.$emit('prevMonth', this.currMonth);
        },
        onDateSelect: function(day) {
            this.$emit('dateSelected', day + '-' + this.currMonth + '-' + this.currYear);
        }
    },
    watch: {
        month: function(oldMonth, newMonth) {
            console.log("month changed");
            var m = Moment().clone();
            m.year(this.year).month(this.month);
            this.fillCalendar(m);
        }
    }
}
</script>

<style scoped>
.today {
    color: #f00;
}

.other-month {
    color: #ccc;
}

i,
td {
    cursor:pointer;
}
</style>
