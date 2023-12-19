<template>
    <div class="mo" @click="activateMonth">
        <span> <img src="../assets/svg/calendar_month.svg" alt="" class="icon-cal"></span>
        <span>{{ displayProper }}</span> 
    </div>
</template>

<script>

export default {
    props: {
        month: Object,
    },
    computed: {
        displayProper() {
            const y = `20${this.month.p.substring(0, 2)}`
            const m = this.month.p.substring(2)
            return `${this.calendar[m]} ${y}`
        }
    },
    inject: [
        'loadMonth', 'calendar', 'setView'
    ],
    methods: {
        async activateMonth() {
            await this.loadMonth(this.month)
            this.setView('cal')
        }
    }
}
</script>

<style scoped>
.mo:first-child
{
    border-top: 1px dotted #cecece;
}

.mo
{
    display: flex;
    padding: 20px 10px;
    border-bottom: 1px dotted #cecece;
    user-select: none;
    gap: 10px;
}

.icon-cal {
    height: 25px;
}
</style>