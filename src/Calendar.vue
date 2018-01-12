<template lang="pug">
    .calendar-container
        .row
            .col-sm-2(@click="prevMonth") Prev
            .col-sm-8.text-center {{month}} {{year}}
            .col-sm-2(@click="nextMonth") Next
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
    data: function() {
        return {
            weeks: [],
            isActive: false,
            month: '',
            year: '',
            days: ['Su', 'Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa']
        }
    },
    created() {
        var m = Moment().clone();
        this.fillCalendar(m);
    },
    methods: {
        fillCalendar: function(m) {
            this.month = m.format('MMM');
            this.year = m.format('Y');
            this.weeks = [];
            m = m.startOf('month').startOf('week').clone();

            var thisDay = Moment().format('D');
            var thisMonth = Moment().format('M');
            var isToday = false;

            var isOtherMonth = false;

            //we want a 7x7 grid starting from the sunday of the week for the 1st of month
            for (var i = 0; i < ROWS; i++) {
                var week = []
                for (var j = 0; j < COLUMNS; j++) {
                    isToday = (thisDay == m.format('D') && thisMonth == m.format('M')) ? true : false;
                    isOtherMonth = (this.month != m.format('MMM')) ? true : false;

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
            m.month(this.month);
            m.add(1, 'month');
            this.fillCalendar(m);
        },
        prevMonth: function() {
            var m = Moment().clone();
            m.month(this.month);
            m.subtract(1, 'month');
            this.fillCalendar(m);
        },
        onDateSelect: function(day) {
            this.$emit('dateSelected', day + '-' + this.month + '-' + this.year);
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

td {
    cursor:pointer
}
</style>
