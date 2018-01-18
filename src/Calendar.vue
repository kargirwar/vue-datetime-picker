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
                                v-bind:class=
                                    "{selected: isSelected(d.moment),\
                                    'other-month': isOtherMonth(d.moment),\
                                    range: isInRange(d.moment)}"
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
        'monthYear': Object,
        'prev': Boolean,
        'next': Boolean,
        'hover': Object
    },
    data: function() {
        return {
            isActive: false,
            days: ['Su', 'Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa'],
            dt1: {},
            dt2: {},
            mY: {},
            firstSelected: false,
            secondSelected: false,
            range: []
        }
    },
    created() {
        this.dt1 = this.date;
        this.dt2 = this.date;
        this.range = [this.dt1, this.dt2];
        this.mY = this.monthYear;
    },
    methods: {
        isOtherMonth: function(m) {
            if (!Moment(m).isSame(this.mY, 'month')) {
                return true;
            } else {
                return false;
            }
        },
        isSelected: function(m) {
            if ((Moment(m).isSame(this.dt1) ||
            Moment(m).isSame(this.dt2)) &&
            (Moment(m).isSame(this.mY, 'month'))) {
                return true;
            }

            return false;
        },
        isInRange: function(m) {
            if (Moment(m).isAfter(this.dt1) &&
                Moment(m).isBefore(this.dt2) &&
                Moment(m).isSame(this.mY, 'month')) {
                return true;
            }
            return false;
        },
        nextMonth: function() {
            var m = Moment(this.mY);
            m.add(1, 'month');
            this.mY = m.toObject()
            this.$emit('nextMonth', this.mY);
        },
        prevMonth: function() {
            var m = Moment(this.mY);
            m.subtract(1, 'month');
            this.mY = m.toObject();
            this.$emit('prevMonth', this.mY);
        },
        onSelect: function(d) {
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
            this.$emit('hover', d.moment);
        },
        onMouseLeave: function(d) {
        },
    },
    watch: {
        monthYear: function(n, o) {
            this.mY = Moment(n).toObject();;
        },
        date: function(n, o) {
            if (!this.firstSelected) {
                this.dt1 = n;
                this.firstSelected = true;
            } else if (!this.secondSelected) {
                if (Moment(n).isAfter(this.dt1)) {
                    this.dt2 = n;
                    this.secondSelected = true;
                } else {
                    this.dt1 = n;
                    this.dt2 = n;
                }
            } else {
                //reset 
                this.dt1 = n;
                this.dt2 = n;
                this.firstSelected = true;
                this.secondSelected = false;
            }

            this.$emit('datesUpdated', {'date1' : this.dt1, 'date2': this.dt2});
        },
    },
    computed: {
        inRange: function() {
            return false;
        },
        weeks: function() {
            var weeks = [];
            var m = Moment(this.mY);
            m = m.startOf('month').startOf('week').clone();

            //we want a 7x7 grid starting from the sunday of the week for the 1st of month
            for (var i = 0; i < ROWS; i++) {
                var week = []
                for (var j = 0; j < COLUMNS; j++) {
                    week.push({
                        moment: m.toObject(),
                    });
                    m.add(1, 'days');
                }

                weeks.push(week);
            }

            return weeks;
        },
        currMonth: function() {
            return Moment(this.mY).format('MMM');
        },
        currYear: function() {
            return Moment(this.mY).format('Y');
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

th,
td {
    padding: 5px;
    font-size: 14px;
}
</style>
