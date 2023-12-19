<template>
    <div class="head">
        <div class="header">
            {{ displayProper }}
        </div>
        <div @click="$emit('setView', 'main')">
            <img class="close-calendar" src="../assets/svg/close.svg" alt="">
        </div>
    </div>
    <div class="summary">
        <div class="target">
            <img src="../assets/svg/track_changes.svg" alt="" class="icon-target">
            <span>Goal:</span>
            <div class="sum-hrs">{{ showTargetHrs }}</div>
        </div>
        <div class="schedule">
            <img src="../assets/svg/schedule.svg" alt="" class="icon-target">
            <span>Sched:</span>
            <div class="sum-hrs"> {{ month.s.s }}h</div>
        </div>

        <div class="schedule" v-show="!isFutureMonth">
            <img src="../assets/svg/check.svg" alt="" class="icon-target">
            <span>Actual:</span>
            <div class="sum-hrs"> {{ month.s.a }}h</div>
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
                <div class="hrs actual">
                    {{ d.a }}
                </div>
            </div>

        </div>
    </div>
    <div id="modal" v-show="view != ''" @click.self="closeModal">
        <div id="sched" v-show="view === 'sched'">
            <!-- <div> {{ sched }}</div> -->
            <div class="dayheader"> {{ getDayHeader() }}</div>
            <div class="field" v-show="isFutureDate">
                <div class="fld-lbl">
                    <img src="../assets//svg/schedule.svg" alt="">
                    <label for="">Plan: </label>
                </div>
                <input type="number" v-model="sched.p">
                
            </div>
            <div class="field" v-show="!isFutureDate">
                <label for="">Actual: </label>
                <input type="number" v-model="sched.a">
            </div>
            <div class="ta-field">
                <label for="">Notes: </label>
                <textarea v-model="sched.n"></textarea>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            days: [],
            view: '',
            sched: { a: 0, p: 0 },
        }
    },
    async mounted() {
        const fd = await this.getMonthProps()
        const cells = 42
        for (let i = 1; i <= cells; i++) {
            const dateKey = i - fd.startDay + 1
            const p = this.month.d[dateKey] ? this.month.d[dateKey].p : ''
            const a = this.month.d[dateKey] ? this.month.d[dateKey].a : ''
            const n = this.month.d[dateKey] ? this.month.d[dateKey].n : ''

            const d = {
                c: i,
                d: fd.startDay <= i && fd.days >= (i - fd.startDay + 1) ? dateKey : '',
                p, a, n,
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
        },
        getDayHeader() {
            if (this.sched.c) {
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
                this.month.s.s -= Number(this.sched.p) || 0
                this.month.s.a -= Number(this.sched.a) || 0
            }
        },
        closeModal() {
            if (this.view == 'sched') {
                this.month.s.s += Number(this.sched.p) || 0
                this.month.s.a += Number(this.sched.a) || 0
                this.saveDate()
            }
            this.view = ''
        },
        saveDate() {
            this.$emit('saveDate', this.month.p, this.sched.d,
                {
                    p: this.sched.p,
                    a: this.sched.a,
                    n: this.sched.n
                })
        },
        isNanValue(value) {
            return isNaN(value);
        },
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
        },
        isFutureDate() {
            const y = `20${this.month.p.substring(0, 2)}`
            const m = this.month.p.substring(2)
            const d = this.sched.d
            const targetdate = new Date(y, m - 1, d);
            const today = new Date()
            return targetdate > today;
        },
        isFutureMonth() {
            const y = `20${this.month.p.substring(0, 2)}`
            const m = this.month.p.substring(2)
            const targetdate = new Date(y, m - 1, 1);
            const today = new Date()
            return targetdate > today;
        }
    },
    inject: [
        'calendar', 'weekDays'
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
    font-size: 12px;
    font-weight: 400;
    gap: 5px;

}

.target span, .schedule span {
    font-size: 12px;
}

.sum-hrs
{
    font-size: 16px;
    font-weight: 600;
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
    height: 50%;
    width: 100%;
    margin: 40px;
    background: white;
    border-radius: 8px;
    padding: 15px;

}

.dayheader
{
    text-align: left;
    font-size: 22px;
    font-weight: 600;
}

.field
{
    display: flex;
    flex-flow: row;
    align-items: baseline;
    gap: 16px;
}

.fld-lbl {
    display: flex;
    align-items: center;
    gap: 8px;
}
.ta-field
{
    display: flex;
    flex-flow: column;
    gap: 5px;
    margin-top: 30px;
}



.field label
{
    width: 60px;
    text-align: left;
    color: gray;
}

.ta-field label
{
    width: 100%;
    text-align: left;
    color: gray;
}

.field input,
.field select
{
    text-align: center;
    border: none;
    padding: 15px 10px 1px 10px;
    font-size: 24px;
    font-weight: 700;
    font-family: 'Poppins', sans-serif;
    border-bottom: 1px solid gray;
    outline: none;
    background: transparent;
    width: 40%;
    box-sizing: border-box;
}

.ta-field textarea
{
    border: none;
    padding: 10px;
    border-radius: 5px;
    outline: none;
    margin: 0px;
    background: rgb(238, 238, 238);
    font-size: 18px;
    font-family: 'Poppins', sans-serif;
    height: 150px;
}
</style>