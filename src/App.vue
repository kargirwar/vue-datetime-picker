<template lang="pug">
    .container-fluid
        b-dropdown(variant='link', size='lg', no-caret='')
            template(slot='button-content')
                input.form-control
            calendar
</template>

<script>
import bModal from 'bootstrap-vue/es/components/modal/modal'
import bButton from 'bootstrap-vue/es/components/button/button';
import bDropdown from 'bootstrap-vue/es/components/dropdown/dropdown';
import Calendar from "./Calendar.vue";

export default {
    data: function() {
        return {
        }
    },
    components: {
        'b-modal': bModal,
        'b-btn': bButton,
        'b-dropdown': bDropdown,
        'calendar': Calendar
    }
}
</script>

<style>
</style>
