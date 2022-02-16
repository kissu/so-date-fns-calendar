<template>
  <div>
    <div>
      <button @click="substractOneMonth">previous</button>
      <span style="margin-left: 2rem">
        current: {{ format(currentMonth, 'MMM yyyy') }}
      </span>
      <button style="margin-left: 2rem" @click="addOneMonth">next</button>
    </div>
    <div class="week-days">
      <p v-for="weekName in weekNames" :key="weekName">{{ weekName }}</p>
    </div>

    <div class="days">
      <p
        v-for="(day, index) in daysOfCurrentMonth"
        :key="day"
        class="day"
        :style="`grid-column: column(${index}); grid-column-start: ${
          index === 0 ? weekdayOffset : '0' // basically first-child with a param
        }; color: ${isToday(day) ? 'red' : 'black'};`"
      >
        {{ format(day, 'dd') }}
      </p>
    </div>

    <button @click="resetToToday">Go back to today</button>
  </div>
</template>

<script>
import {
  startOfMonth,
  addMonths,
  format,
  subMonths,
  addDays,
  startOfWeek,
  sub,
  add,
  eachDayOfInterval,
  getDay,
  isToday,
} from 'date-fns'

export default {
  data() {
    return {
      currentMonth: startOfMonth(new Date()),
      firstDayOfWeek: startOfWeek(new Date()),
    }
  },
  computed: {
    previousMonth() {
      return startOfMonth(subMonths(new Date(this.currentMonth), 1))
    },
    nextMonth() {
      return startOfMonth(addMonths(new Date(this.currentMonth), 1))
    },
    daysOfCurrentMonth() {
      return eachDayOfInterval({
        start: this.currentMonth,
        end: sub(this.nextMonth, { days: 1 }),
      })
    },
    weekNames() {
      return [...Array(7)].map((_, index) =>
        format(addDays(this.firstDayOfWeek, index), 'EEEEEE')
      )
    },
    weekdayOffset() {
      return (getDay(this.currentMonth) + 8) % 7 || 7 // `|| 7` is basically for sunday, edge case
    },
  },
  methods: {
    format,
    isToday,

    substractOneMonth() {
      this.currentMonth = sub(this.currentMonth, { months: 1 })
    },
    addOneMonth() {
      this.currentMonth = add(this.currentMonth, { months: 1 })
    },
    resetToToday() {
      this.currentMonth = startOfMonth(new Date())
    },
  },
}
</script>

<style scoped>
.days,
.week-days {
  display: grid;
  grid-template-columns: repeat(7, 50px);
  grid-column: 7;
}
</style>
