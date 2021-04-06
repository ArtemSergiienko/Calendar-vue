<template lang="pug">
  .calendar
    h1.calendar__title Календарь
    .calendar__wrap
      .calendar__head
        .calendar__btn.calendar__btn--prev(
          :class="isDisabledPrev ? 'calendar__btn--disabled' : ''"
          @click="getPrevMonth"
        )
          include prev-icon.svg
        .calendar__month {{isMonth}}
        .calendar__btn.calendar__btn--next(
          :class="isDisabledNext ? 'calendar__btn--disabled' : ''"
          @click='getNextMonth'
        )
          include next-icon.svg
        .calendar__year {{watchYear}}
      .calendar__inner
        .calendar__week
          .calendar__week-item ПН.
          .calendar__week-item ВТ.
          .calendar__week-item СР.
          .calendar__week-item ЧТ.
          .calendar__week-item ПТ.
          .calendar__week-item СБ.
          .calendar__week-item ВС.
        .calendar__days
          .calendar__day(
            v-if='firstDay'
            v-for="value in firstDay"
          )

          .calendar__day(
            v-for="(value, index) in objectsDays"
          )
            label.calendar__label
              input.calendar__radio(
                type="radio"
                name='day'
              )
              .calendar__label-info
                .calendar__day-count {{value.day}}
</template>

<script>
  export default {
    name: 'Calendar',
    data() {
      return {
        dayToday: null,
        monthToday: null,
        yearToday: null,
        isDisabledPrev: true,
        isDisabledNext: false,
        firstDay: null,
        watchMonth: null,
        watchYear: null,
        calendar: [],
        objectsDays: {},
        arrayMonth: [
          'Январь',
          'Февраль',
          'Март',
          'Апрель',
          'Май',
          'Июнь',
          'Июль',
          'Август',
          'Сентябрь',
          'Октябрь',
          'Ноябрь',
          'Декабрь',
        ],
        isMonth: null,
        dataDay: {
          tableId: null,
          from: null
        }
      }
    },
    mounted() {
      this.getTodayDate();
      this.createCalendar(this.yearToday, this.monthToday);
    },
    methods: {
      getTodayDate() {
        const date = new Date();
        const todayDate = new Date(date.getTime() - (date.getTimezoneOffset() * 60000)).toISOString().substring(0, 16).replace('T', ' ');
        // eslint-disable-next-line no-useless-escape
        const arrayTodayDate = todayDate.split(/[' '\/ -]/, 3);
        this.yearToday = parseInt(arrayTodayDate[0]);
        this.monthToday = parseInt(arrayTodayDate[1]);
        this.dayToday = parseInt(arrayTodayDate[2]);
        this.watchYear = parseInt(arrayTodayDate[0]);
        this.isMonth = this.arrayMonth[this.monthToday - 1];
      },

      createCalendar(year, month) {
        this.watchMonth = month;
        this.watchYear = year;
        let mon = month - 1;
        let d = new Date(year, mon);
        this.isMonth = this.arrayMonth[mon];
        if(this.monthToday === this.watchMonth && this.yearToday === this.watchYear) {
          this.isDisabledPrev = true;
        }

        // получение дня недели с которого начинается месяц
        const getDay = (date) => {
          let day = date.getDay();
          if (day == 0) day = 7;
          this.firstDay = day - 1;
        };
        getDay(d);
        this.calendar = [];
        // eslint-disable-next-line no-unused-vars
        for (let i = 0; d.getMonth() == mon; d.getMonth() + 1) {
          this.calendar.push(d.getDate());
          d.setDate(d.getDate() + 1);
        }
        this.getDaysCalendar(year,month);
      },
      getNextMonth() {
        if(!this.isDisabledNext) {
          this.isDisabledPrev = false;
          this.calendar = [];
          this.objectsDays = {};
          this.watchMonth++;
          this.createCalendar(this.watchYear, this.watchMonth);

          if(this.watchMonth > 12) {
            this.calendar = [];
            this.objectsDays = {};
            this.watchMonth = null;
            this.watchMonth++;
            this.watchYear = this.watchYear + 1;
            this.createCalendar(this.watchYear, this.watchMonth);
          }
        }
      },
      getPrevMonth() {
        if(!this.isDisabledPrev) {
          this.calendar = [];
          this.objectsDays = {};
          this.watchMonth--;
          this.createCalendar(this.watchYear, this.watchMonth);
        }
        if(this.watchMonth < 1) {
          this.calendar = [];
          this.objectsDays = {};
          this.watchMonth = null;
          this.watchMonth = 12;
          this.watchYear = this.watchYear - 1;
          this.createCalendar(this.watchYear, this.watchMonth);
        }
      },
      getDaysCalendar() {
        this.calendar.forEach((item, index) => {
          this.objectsDays[index] = {
            'day': item,
            'active': false
          };
        });
      },
    }
  };
</script>

<style lang="sass">
  @import 'index.sass'
</style>
