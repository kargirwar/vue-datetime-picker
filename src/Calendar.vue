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
                            td(
                                v-bind:class="d.style"
                                v-for="d in week"
                                @click="onSelect(d)"
                                @mouseover="onMouseOver(d)"
                                @mouseleave="onMouseLeave(d)")
                                    span {{d.moment.date}}

</template>

<script>
import Moment from 'moment';
const ROWS = 6;
const COLUMNS = 7;

export default {
    props: {
        'date': Object,
        'daterange': Boolean,
        'prev': Boolean,
        'next': Boolean
    },
    data: function() {
        return {
            weeks: [],
            isActive: false,
            selectedDate: {},
            currMonth: '',
            currYear: '',
            days: ['Su', 'Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa'],
            dt: {}
        }
    },
    created() {
        this.dt = this.date;
        this.fillCalendar(Moment(this.dt));
    },
    methods: {
        fillCalendar: function(m) {
            this.currMonth = m.format('MMM');
            this.currYear = m.format('Y');
            this.weeks = [];
            m = m.startOf('month').startOf('week').clone();

            var isToday = false;
            var isOtherMonth = false;
            var isSelected = false;

            //we want a 7x7 grid starting from the sunday of the week for the 1st of month
            for (var i = 0; i < ROWS; i++) {
                var week = []
                for (var j = 0; j < COLUMNS; j++) {
                    isToday = Moment().isSame(m, 'day');
                    isOtherMonth = m.isSame(this.dt, 'month') ? false : true;
                    isSelected = m.isSame(this.selectedDate) ? true : false;;

                    week.push({
                        moment: m.toObject(),
                        style: {
                            'today': isToday,
                            'other-month': isOtherMonth,
                            'selected': isSelected,
                            'range': false
                        }
                    });
                    m.add(1, 'days');
                }

                this.weeks.push(week);
            }
        },
        nextMonth: function() {
            var m = Moment(this.dt);
            m.add(1, 'month');
            this.dt = m.toObject();
            this.fillCalendar(m);
            this.$emit('nextMonth', this.dt);
        },
        prevMonth: function() {
            var m = Moment(this.dt);
            m.subtract(1, 'month');
            this.dt = m.toObject();
            this.fillCalendar(m);
            this.$emit('prevMonth', this.dt);
        },
        onSelect: function(d) {
            this.unSelectOthers(); 
            //select this
            d.style.selected = true;
            this.selectedDate = d.moment;
            this.$emit('dateSelected', d.moment);
        },
        unSelectOthers: function() {
            for (var i = 0; i < this.weeks.length; i++) {
                for (var j = 0; j < this.weeks[i].length; j++) {
                    this.weeks[i][j].style.selected = false;
                }
            }
        },
        onMouseOver: function(d) {
            //apply range style to all intervening days
            for (var i = 0; i < this.weeks.length; i++) {
                for (var j = 0; j < this.weeks[i].length; j++) {
                    var o = this.weeks[i][j];
                    if (Moment(o.moment).isAfter(this.selectedDate) &&
                        Moment(o.moment).isBefore(d.moment)) {
                        o.style.range = true;
                    } else {
                        o.style.range = false;
                    }
                }
            }
        },
        onMouseLeave: function(d) {
        }
    },
    watch: {
        date: function(n, o) {
            if (Moment(n).isSame(Moment(this.dt))) {
                return;
            }
            console.log('old date: ' + o.months + '-' + o.years);
            console.log('new date: ' + n.months + '-' + n.years);
            this.dt = Moment(n).toObject();;
            this.fillCalendar(Moment(this.dt));
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

.selected {
    background: #357ebd;
    color:#fff
}
td:hover {
    background: #eee;
}
.range {
    background: #ebf4f8;
}
</style>
