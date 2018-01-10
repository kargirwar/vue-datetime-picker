<template lang="pug">
    table.table
        tr
            td(v-for="(d, i) in weeks[0]")
                span(v-bind:class="{active: d['active']}") {{d['day']}}
        tr
            td(v-for="(d, i) in weeks[1]")
                span(v-bind:class="{active: d['active']}") {{d['day']}}

        tr
            td(v-for="(d, i) in weeks[2]")
                span(v-bind:class="{active: d['active']}") {{d['day']}}
        tr
            td(v-for="(d, i) in weeks[3]")
                span(v-bind:class="{active: d['active']}") {{d['day']}}

        tr
            td(v-for="(d, i) in weeks[4]")
                span(v-bind:class="{active: d['active']}") {{d['day']}}
        tr
            td(v-for="(d, i) in weeks[5]")
                span(v-bind:class="{active: d['active']}") {{d['day']}}
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
            isActive: false
        }
    },
    created() {
        var m = Moment().startOf('month').startOf('week').clone();
        m.subtract(1, 'days');
        var thisDay = Moment().format('D');
        var thisMonth = Moment().format('M');
        var isActive = false;
        //we want a 7x7 grid starting from the sunday of the week for the 1st of month
        for (var i = 0; i < ROWS; i++) {
            var week = []
            for (var j = 0; j < COLUMNS; j++) {
                m.add(1, 'days');
                if (thisDay == m.format('D') && thisMonth == m.format('M')) {
                    isActive = true;
                } else {
                    isActive = false;
                }
                week.push({
                    'day': m.format('D'),
                    'active': isActive
                });
                console.log(m.format('D'));
            }

            this.weeks.push(week);
        }
    }
}
</script>

<style scoped>
.active {
    color: #f00;
    display:block;
}
</style>
