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
        'monthYear': Object,
        'prev': Boolean,
        'next': Boolean,
        'hover': Object
    },
    data: function() {
        return {
            weeks: [],
            isActive: false,
            days: ['Su', 'Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa'],
            dt1: {},
            dt2: {},
            mY: {},
            firstSelected: false,
            secondSelected: false,
        }
    },
    created() {
        this.dt1 = this.date;
        this.dt2 = this.date;
        this.mY = this.monthYear;
        this.fillCalendar(Moment(this.mY));
    },
    methods: {
        fillCalendar: function(m) {
            this.weeks = [];
            m = m.startOf('month').startOf('week').clone();

            var isToday = false;
            var isOtherMonth = false;
            var isSelected = false;

            //we want a 7x7 grid starting from the sunday of the week for the 1st of month
            for (var i = 0; i < ROWS; i++) {
                var week = []
                for (var j = 0; j < COLUMNS; j++) {
                    //isToday = Moment().isSame(m, 'day');
                    //isOtherMonth = m.isSame(this.mY, 'month') ? false : true;
                    //isSelected = (m.isSame(this.dt1) || m.isSame(this.dt2)) ? true : false;

                    week.push({
                        moment: m.toObject(),
                        style: this.getStyle(m.toObject())
                    });
                    m.add(1, 'days');
                }

                this.weeks.push(week);
            }
        },
        nextMonth: function() {
            var m = Moment(this.mY);
            m.add(1, 'month');
            this.mY = m.toObject()
            this.fillCalendar(Moment(this.mY));
            this.$emit('nextMonth', this.mY);
        },
        prevMonth: function() {
            var m = Moment(this.mY);
            m.subtract(1, 'month');
            this.mY = m.toObject();
            this.fillCalendar(Moment(this.mY));
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
        getStyle: function(m) {
            var style = {};
            if ((Moment(m).isSame(this.dt1) ||
                Moment(m).isSame(this.dt2)) &&
                (Moment(m).isSame(this.mY, 'month'))) {
                style.selected = true;
            } else {
                style.selected = false;
            }

            //add range classes
            if (Moment(m).isAfter(this.dt1) &&
                Moment(m).isBefore(this.dt2) &&
                Moment(m).isSame(this.mY, 'month')) {
                style.range = true;
            } else {
                style.range = false;
            }

            if (!Moment(m).isSame(this.mY, 'month')) {
                style['other-month'] = true;
            } else {
                style['other-month'] = false;
            }

            return style;
        }
    },
    watch: {
        monthYear: function(n, o) {
            this.mY = Moment(n).toObject();;
            this.fillCalendar(Moment(this.mY));
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

            //update selected date and range formatting
            for (var i = 0; i < this.weeks.length; i++) {
                for (var j = 0; j < this.weeks[i].length; j++) {
                        var d = this.weeks[i][j];
                        //add selected classes
                        if ((Moment(d.moment).isSame(this.dt1) ||
                            Moment(d.moment).isSame(this.dt2)) &&
                            (Moment(d.moment).isSame(this.mY, 'month'))) {
                            d.style.selected = true;
                        } else {
                            d.style.selected = false;
                        }

                        //add range classes
                        if (Moment(d.moment).isAfter(this.dt1) &&
                            Moment(d.moment).isBefore(this.dt2) &&
                            Moment(d.moment).isSame(this.mY, 'month')) {
                                d.style.range = true;
                        } else {
                                d.style.range = false;
                        }
                }
            }
        },

        hover: function(n, o) {
            if (Moment(this.dt2).isAfter(this.dt1)) {
                return;
            }

            //if selected date is in next month we don't show the range style
            if (Moment(this.dt1).isAfter(this.monthYear, 'month')) {
                console.log('hiding');
                for (var i = 0; i < this.weeks.length; i++) {
                    for (var j = 0; j < this.weeks[i].length; j++) {
                        var o = this.weeks[i][j];
                        o.style.range = false;
                    }
                }
                return;
            }
            //apply range style to all intervening days
            console.log('hover');
            for (var i = 0; i < this.weeks.length; i++) {
                for (var j = 0; j < this.weeks[i].length; j++) {
                    var o = this.weeks[i][j];
                    if (Moment(o.moment).isAfter(this.dt1) &&
                        Moment(o.moment).isBefore(n) && Moment(o.moment).isSame(this.mY, 'month')) {
                        o.style.range = true;
                    } else {
                        o.style.range = false;
                    }
                }
            }
        }
    },
    computed: {
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
</style>
