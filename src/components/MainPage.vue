<template>
    <div class="head">
        <div class="header">
            {{ profile.name}}
        </div>
        <div @click="$emit('setView', 'profile')">
            <span class="material-symbols-outlined">
                account_circle
            </span>
        </div>
    </div>
    <div class="rank">
        <span>{{ rank }}</span>
    </div>
    <div class="months-list">
        <template v-for="m in months" :key="m.p" >
            <PeriodItem :month="m"></PeriodItem>
        </template>
    </div>
</template>

<script>
import PeriodItem from './PeriodItem.vue'
export default {
    components: {
        PeriodItem
    },
    props: {
        profile: Object,
        months: Array,
    },
    computed: {
        rank() {
            const ranks = {
                ap: 'Auxillary Pioneer', rp: 'Regular Pioneer'
            }

            return ranks[this.profile.rank]
        },

    }
}
</script>

<style scoped>
.rank {
    font-size: 12px;
    text-align: left;
    padding: 0 10px;
}


.months-list {
    margin-top: 30px;
}


</style>