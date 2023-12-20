<template>
  <ProfileForm v-if="view.live === 'profile'" :profile="profile" :months="months" @set-view="setView"
    @store-reset="storeReset" @optimize-store="optimizeMonthsStore" @backup="restoreBackup">
  </ProfileForm>
  <MainPage v-if="view.live === 'main'" :profile="profile" :months="months" @set-view="setView"></MainPage>
  <CalendarMonth v-if="view.live === 'cal'" :month="month" @set-view="setView" @save-date="saveDate"></CalendarMonth>
</template>


<script>
import ProfileForm from './components/ProfileForm.vue';
import MainPage from './components/MainPage.vue';
import CalendarMonth from './components/CalendarMonth.vue';

const calendar = {
  '01': 'January',
  '02': 'February',
  '03': 'March',
  '04': 'April',
  '05': 'May',
  '06': 'June',
  '07': 'July',
  '08': 'August',
  '09': 'September',
  '10': 'October',
  '11': 'November',
  '12': 'December'
}

const weekDays = {
  '1': { 'ddd': 'Sunday', 'dd': 'Sun' },
  '2': { 'ddd': 'Monday', 'dd': 'Mon' },
  '3': { 'ddd': 'Tuesday', 'dd': 'Tue' },
  '4': { 'ddd': 'Wednesday', 'dd': 'Wed' },
  '5': { 'ddd': 'Thursday', 'dd': 'Thu' },
  '6': { 'ddd': 'Friday', 'dd': 'Fri' },
  '0': { 'ddd': 'Saturday', 'dd': 'Sat' },
}

export default {
  components: {
    ProfileForm,
    MainPage,
    CalendarMonth,

  },
  data() {
    return {
      view: {
        live: 'main',
      },
      month: null,
      profile: {
        name: null,
        rank: null
      },
      months: [],
      holdStore: false,
    }
  },
  async mounted() {
    const prof = await this.getStored('prof')
    const months = await this.getStored('months')

    if (prof && prof.name != null && prof.rank != null) {
      this.profile = prof
    } else {
      this.view.live = 'profile'
    }

    if (months) {
      this.months = months
    } else {
      this.months = [
        {
          p: '2403',
          d: {
            // 2: { p: 3, a: 2, n: null }, 9: { p: 3, a: 2, n: null }
          }, t: 50, s: { s: 0, a: 0 }
        },
        {
          p: '2402', d: {}, t: 50, s: { s: 0, a: 0, n: null }
        },
        {
          p: '2401', d: {}, t: 50, s: { s: 0, a: 0, n: null }
        },
        {
          p: '2312', d: {}, t: 50, s: { s: 0, a: 0, n: null }
        },
        {
          p: '2311', d: {}, t: 50, s: { s: 0, a: 0, n: null }
        },
      ]
    }

  },
  methods: {
    setView(view) {
      this.view.live = view;
    },
    async loadMonth(p) {
      this.month = p
    },
    async saveDate(month, date, d) {
      for (let i = 0; i < this.months.length; i++) {
        const m = this.months[i];
        if (m.p == month) {
          m.d[date] = d;
          return;
        }
      }
    },
    storeProfile() {
      if (!this.holdStore) {
        localStorage.setItem('prof', JSON.stringify(this.profile))
      }
    },
    async storeMonths() {
      if (!this.holdStore) {
        await this.optimizeMonthsStore()
        localStorage.setItem('months', JSON.stringify(this.months))
      }
    },
    async optimizeMonthsStore() {
      this.holdStore = true
      for (let i = 0; i < this.months.length; i++) {
        const month = this.months[i];
        const dates = month.d;
        for (const key in dates) {
          if (Object.hasOwn(dates, key)) {
            const day = dates[key];

            if (day.p == '' && day.a == '' && day.n == '') {
              delete dates[key];
            }
          }
        }
      }
      this.holdStore = false
    },
    async getStored(key) {
      const v = localStorage.getItem(key)
      return (v) ? JSON.parse(v) : null
    },
    storeReset() {
      this.profile = {
        name: null,
        rank: null
      }

      localStorage.removeItem('prof')
      localStorage.removeItem('months')
    },
    async restoreBackup(data) {
      this.profile = data.profile
      this.months = data.months
    }
  },
  provide() {
    return {
      loadMonth: this.loadMonth,
      calendar,
      weekDays,
    }
  },
  watch: {
    profile: {
      handler: 'storeProfile',
      deep: true
    },
    months: {
      handler: 'storeMonths',
      deep: true
    }
  }

}
</script>

<style>
.head
{
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.header
{
  font-size: 24px;
  font-weight: 700;
}
</style>
