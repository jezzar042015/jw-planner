<template>
    <div class="head">
        <div class="header">
            {{ displayProper }}
        </div>
        <div @click="setView('main')">
            <img class="close-calendar" src="../assets/svg/close.svg" alt="">
        </div>
    </div>
    <div class="summary">
        <div class="target">
            <img src="../assets/svg/track_changes.svg" alt="" class="icon-target">
            <span>Goal:</span>
            <div>{{ showTargetHrs }}</div>
        </div>
        <div class="schedule">
            <img src="../assets/svg/schedule.svg" alt="" class="icon-target">
            <span>Sched:</span>
            <div> {{ month.s.s }}h</div>
        </div>

        <div class="schedule">
            <img src="../assets/svg/check.svg" alt="" class="icon-target">
            <span>Actual:</span>
            <div> {{ month.s.a }}h</div>
        </div>
    </div>
    <div class="cal">
        <div class="calendar">
            <div class="dayname">S</div>
            <div class="dayname">M</div>
            <div class="dayname">T</div>
            <div class="dayname">W</div>
            <div class="dayname">T</div>
            <div class="dayname">F</div>
            <div class="dayname">S</div>
            <div v-for="d in days" :key="d.c" class="date" @click.stop="setSched(d)">
                <div class="dateno">{{ d.d }}</div>
                <div class="hrs plan">
                    {{ d.p }}
                </div>

            </div>
        </div>
    </div>
    <div id="modal" v-show="view != ''" @click.self="closeModal">
        <div id="sched" v-show="view === 'sched'">
            <div class="dayheader"> {{ getDayHeader() }}</div>
            <div>

                <label for="">Plan: </label>
                <input type="number" name="" id="">
            </div>
            <label for="">Actual: </label>
            <input type="number" name="" id="">
            <div> {{ sched }}</div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            days: [],
            view: '',
            sched: null,
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

            // this.month.s.s += Number(this.month.d[dateKey].p);

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
        },
        getDayHeader() {
            if (this.sched) {                
                const m = this.month.p.substring(2)
                const dateNum = this.sched.c % 7
                return `${this.calendar[m]} ${this.sched.d}, ${this.weekDays[dateNum].ddd}`
            } else {
                return null
            }
        },
        setSched(d) {
            if (d.d) {
                this.view = 'sched'
                this.sched = d
            }
        },
        closeModal() {
            this.view = ''
        }
    },
    computed: {
        displayProper() {
            const y = `20${this.month.p.substring(0, 2)}`
            const m = this.month.p.substring(2)
            return `${this.calendar[m]} ${y}`
        },
        showTargetHrs() {
            return (this.month.t) ?
                `${this.month.t}h` : 'No Set Target'

        }
    },
    inject: [
        'calendar', 'setView', 'weekDays'
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

.calendar .date:nth-child(n)
{
    border-right: 1px solid #cccccc;
    border-bottom: 1px solid #cccccc;
}

.calendar .date:nth-child(7n + 1)
{
    border-left: 1px solid #cccccc;
}


.calendar .date:not(:nth-child(n))
{
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
    user-select: none;

    display: flex;
    flex-flow: column;
}

.hrs
{
    font-size: 22px;
    font-weight: 600;
    text-align: center;

    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
}

.plan
{
    color: rgb(158, 158, 158);
}

.actual
{
    color: rgb(1, 1, 136);

}

.close-calendar
{
    height: 30px;
}

.summary
{
    display: flex;
    justify-content: space-around;
}

.target,
.schedule
{
    display: flex;
    align-items: center;
    padding: 12px 0 0;
    font-size: 15px;
    font-weight: 400;
    gap: 5px;

}

.icon-target
{
    height: 20px;
}

#modal
{
    position: absolute;
    top: 0;
    left: 0;
    background: #000000b6;
    height: 100dvh;
    width: 100%;

    display: flex;
    justify-content: center;
    align-items: center;
    padding: 0 0px;
}

#sched
{
    height: 70%;
    width: 100%;
    margin: 20px;
    background: white;
    border-radius: 8px;
    padding: 15px;

}

.dayheader {
    /* background: yellow; */
    text-align: left;
    font-size: 22px;
}
</style>