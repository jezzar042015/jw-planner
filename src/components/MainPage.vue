<template>
    <div class="head">
        <div class="header">
            {{ profile.name}}
        </div>
        <div @click="$emit('setView', 'profile')">
            <img src="../assets/svg/account_circle.svg" alt="" class="icon-profile">
        </div>
    </div>
    <div class="rank">
        <span>{{ rank }}</span>
    </div>
    <div class="months-list">
        <template v-for="m in months" :key="m.p" >
            <PeriodItem :month="m" @set-view="setView"></PeriodItem>
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

    },
    methods: {
        setView(n) {
            this.$emit('setView', n)
        }
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

.icon-profile {
    height: 40px;
}


</style>