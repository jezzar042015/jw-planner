<template>
  <ProfileForm v-if="view.live === 'profile'" :profile="profile" @set-view="setView"></ProfileForm>
  <MainPage v-if="view.live === 'main'" :profile="profile" :months="months" @set-view="setView"></MainPage>
  <CalendarMonth v-if="view.live === 'cal'" :month="month"></CalendarMonth>
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
  '1': { 'ddd': 'Sunday', 'dd': 'Sun'},
  '2': { 'ddd': 'Monday', 'dd': 'Mon'},
  '3': { 'ddd': 'Tuesday', 'dd': 'Tue'},
  '4': { 'ddd': 'Wednesday', 'dd': 'Wed'},
  '5': { 'ddd': 'Thursday', 'dd': 'Thu'},
  '6': { 'ddd': 'Friday', 'dd': 'Fri'},
  '0': { 'ddd': 'Saturday', 'dd': 'Sat'},
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
      months: [
        {
          p: '2403',
          d: { 2: { p: 3, a: 2 }, 9: { p: 3, a: 2 } },
          t: 50, s: {s: 6, a: 0}
        },
        { p: '2402', d: { 5: { p: 3, a: 2 } }, t: 50, s: {s: 0, a: 0} },
        { p: '2401', d: { 5: { p: 3, a: 2 } }, t: 50, s: {s: 0, a: 0} },
        { p: '2312', d: { 5: { p: 3, a: 2 } }, t: 50, s: {s: 0, a: 0} },
      ]
    }
  },
  async mounted() {
    const prof = await this.getStored('prof')
    if (prof) {
      this.profile = prof
    } else {
      this.view.live = 'profile'
    }
  },
  methods: {
    setView(view) {
      this.view.live = view;
    },
    async loadMonth(p) {
      this.month = p
    },
    storeProfile() {
      localStorage.setItem('prof', JSON.stringify(this.profile))
    },
    async getStored(key) {
      const v = localStorage.getItem(key)
      return (v) ? JSON.parse(v) : null
    }
  },
  provide() {
    return {
      loadMonth: this.loadMonth,
      setView: this.setView,
      calendar,
      weekDays,
    }
  },
  watch: {
    profile: {
      handler: 'storeProfile',
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
