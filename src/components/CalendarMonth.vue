<template>
    <div class="head">
        <div class="header">
            {{ displayProper }}
        </div>
        <div @click="setView('main')">
            <span class="material-symbols-outlined">
                close
            </span>
        </div>
    </div>
    <!-- <div>{{ getMonthProps().days }}</div> -->
    <div class="cal">
        <div class="calendar">
            <div class="dayname">S</div>
            <div class="dayname">M</div>
            <div class="dayname">T</div>
            <div class="dayname">W</div>
            <div class="dayname">T</div>
            <div class="dayname">F</div>
            <div class="dayname">S</div>
            <div v-for="d in days" :key="d.c" class="date">
                <div class="dateno">{{ d.d }}</div>
                <div class="hrs plan">
                        {{ d.p }}
                </div>
                
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            days: []
        }
    },
    async mounted() {
        const fd = await this.getMonthProps()
        const cells = 42
        for (let i = 1; i <= cells; i++) {
            const dateKey = i - fd.startDay + 1
            const d = {
                c: i,
                d: fd.startDay <= i && fd.days >= (i - fd.startDay + 1)
                    ? dateKey
                    : '',
                p: this.month.d[dateKey] ? this.month.d[dateKey].p : '' 
            }

            if (i == 36 && d.d === '') {
                return
            }

            this.days.push(d)
        }
    },
    props: {
        month: Object,
    },
    methods: {
        async getMonthProps() {
            const y = `20${this.month.p.substring(0, 2)}`
            const m = this.month.p.substring(2)
            const mo = `${this.calendar[m]} ${y}`

            const d = new Date(`${mo} 1, ${y}`)
            const nd = new Date(parseInt(y), parseInt(m), 0)          
            return {
                startDay: d.getDay() + 1,
                days: nd.getDate()
            }
        }
    },
    computed: {
        displayProper() {
            const y = `20${this.month.p.substring(0, 2)}`
            const m = this.month.p.substring(2)
            return `${this.calendar[m]} ${y}`
        },
    },
    inject: [
        'calendar', 'setView'
    ],
}
</script>

<style scoped>
.calendar
{
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    grid-gap: 0px;
    margin-top: 25px;
}

.dayname
{
    height: 20px;
    font-weight: 600;
    background: darkslategray;
    color: white;
    padding: 10px;
    text-align: center;
    font-size: 12px;
}

.calendar .date:nth-child(n) {
    border-right: 1px solid #cccccc;
    border-bottom: 1px solid #cccccc;
}

.calendar .date:nth-child(7n + 1) {
    border-left: 1px solid #cccccc;
}


.calendar .date:not(:nth-child(n)) {
    border-left: none;
}

.date
{
    background-color: white;
    height: 80px;
    font-size: 9px;
    font-weight: 300;
    text-align: left;
    padding: 5px;
    color: #777777;

    display: flex;
    flex-flow: column;
}

.hrs {
    font-size: 22px;
    font-weight: 600;
    text-align: center;
    
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
}

.plan {
    color: rgb(158, 158, 158);
}
.actual {
    color: rgb(1, 1, 136);

}

</style>