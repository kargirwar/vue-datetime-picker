<template lang="pug">
    .datepicker-container
        input.form-control(:value="selectedDate" ref="input" @click="showPopper = !showPopper")
        .dropdown-menu(ref="dropdown" :class="{show:showPopper}")
            .container
                .row(v-if="daterange")
                    .col-sm-6
                        input.form-control(:value="date1")
                    .col-sm-6
                        input.form-control(:value="date2")
                .row(v-if="daterange")
                    .col-sm-6
                        calendar(
                                v-on:dateSelected="onDate1Select"
                                v-on:datesUpdated="onDateUpdate"
                                v-on:prevMonth="onPrevMonth"
                                v-on:hover="onHover"
                                :hover="hd"
                                :date="dt"
                                :monthYear="mY1"
                                :prev="daterange"
                                :next="!daterange"
                            )
                    .col-sm-6
                        calendar(
                                v-on:dateSelected="onDate2Select"
                                v-on:nextMonth="onNextMonth"
                                v-on:hover="onHover"
                                :hover="hd"
                                :date="dt"
                                :monthYear="mY2"
                                :prev="!daterange"
                                :next="daterange"
                            )
                .row(v-if="daterange")
                    .col-sm-4
                        button.btn.btn-success.btn-sm(@click="onApply") Apply
                        button.btn.btn-outline-secondary.btn-sm(@click="onCancel") Cancel
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
import Popper from 'popper.js';

export default {
    props: {
        daterange: {
            type: Boolean,
            required: true
        }
    },
    data: function() {
        return {
            mY1: {},
            mY2: {},
            mY: {},
            dt1: {},
            dt2: {},
            dt: {},
            hd: {},
            show: true,
            showPopper: false,
            selectedDate: ''
        }
    },
    components: {
        'calendar': Calendar,
    },
    methods: {
        onDateSelect: function(m) {
            //this.selectedDate = Moment(m).format('Y-MM-DD');
            //close the dropdown
            this.$refs.input.click();
        },
        onDate1Select: function(dt) {
            //copy to next month
            this.dt1 = dt;
            this.dt = dt;
        },
        onDate2Select: function(dt) {
            //copy to prev month
            this.dt2 = dt;
            this.dt = dt;
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
        },
        onHover: function(hd) {
            this.hd = hd;
        },
        onDateUpdate: function(o) {
            this.dt1 = o.date1;
            this.dt2 = o.date2;
        },
        onApply: function() {
            this.showPopper = false;
            this.$emit('dateChanged', {dt1: this.dt1, dt2: this.dt2});
            this.selectedDate = Moment(this.dt1).format('Y-MM-D') + '-' + Moment(this.dt2).format('Y-MM-D');
        },

        onCancel: function() {
            this.showPopper = false;
        }
    },
    computed: {
        date1: function() {
            return Moment(this.dt1).format('D/MMM/Y');
        },
        date2: function() {
            return Moment(this.dt2).format('D/MMM/Y');
        },
        //selectedDate: function() {
            //return Moment(this.dt1).format('D/MMM/Y') + '-' + Moment(this.dt2).format('D/MMM/Y');
        //}
    },
	mounted: function() {
		var reference = this.$refs.input;
		var popper = this.$refs.dropdown
		var anotherPopper = new Popper(reference, popper, {});        
	},
    created: function() {

		if (this.daterange) {
			//two calendars, this and next month
            var m = Moment().clone();
            this.mY1 = m.toObject();
            this.mY2 = m.add(1, 'month').toObject();
            return;
        }

        this.mY = Moment().clone().toObject();
    }
}
</script>

<style scoped>
.dropdown-menu {
    min-width: 600px;
}
.btn-success {
    margin-right: 5px;
}
</style>
