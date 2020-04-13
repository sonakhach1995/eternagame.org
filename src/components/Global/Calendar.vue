<template id='calendar'>
  <div class='calendar'>
    <div class='header'>
      <!--      <a class='arrow' @click='movePreviousYear'>&laquo;</a>-->
      <a class='arrow' @click='movePreviousMonth'>&lsaquo;</a>
      <span class='title' @click='moveThisMonth'>
        {{ header.label }}
      </span>
      <a class='arrow' @click='moveNextMonth'>&rsaquo;</a>
      <!--      <a class='arrow' @click='moveNextYear'>&raquo;</a>-->
    </div>
    <div class="year">{{ year }}</div>
    <div class='weekdays'>
      <div class='weekday' v-for='weekday in weekdays'>
        {{ weekday.label_3 }}
      </div>
    </div>
    <div class='week' v-for='week in weeks'>
      <div
        class='day' @click="clickOnDay(day)"
        :class='{ today: day.isToday, "not-in-month": !day.inMonth }'
        v-for='day in week'>
        {{ day[dayKey] }}
      </div>
    </div>
  </div>
</template>

<script>
  // Calendar data
  const _daysInMonths = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
  const _weekdayLabels = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
  const _monthLabels = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
  const _today = new Date();
  const _todayComps = {
    year: _today.getFullYear(),
    month: _today.getMonth() + 1,
    day: _today.getDate(),
  };

  export default {
    name: 'calendar',
    data() {
      return {
        month: _todayComps.month,
        year: _todayComps.year,
        day: _todayComps.day,

        value: _today,
      };
    },
    props: {
      dayKey: { type: String, default: 'label' },
    },
    computed: {
      // Our component exposes month as 1-based, but sometimes we need 0-based
      monthIndex() {
        return this.month - 1;
      },
      isLeapYear() {
        return (this.year % 4 === 0 && this.year % 100 !== 0) || this.year % 400 === 0;
      },
      // Day/month/year components for previous month
      previousMonthComps() {
        if (this.month === 1) {
          return {
            days: _daysInMonths[11],
            month: 12,
            year: this.year - 1,
          };
        }
        return {
          days: (this.month === 3 && this.isLeapYear) ? 29 : _daysInMonths[this.month - 2],
          month: this.month - 1,
          year: this.year,
        };
      },
      // Day/month/year components for next month
      nextMonthComps() {
        if (this.month === 12) {
          return {
            days: _daysInMonths[0],
            month: 1,
            year: this.year + 1,
          };
        }
        return {
          days: (this.month === 2 && this.isLeapYear) ? 29 : _daysInMonths[this.month],
          month: this.month + 1,
          year: this.year,
        };
      },
      // State for calendar header (no dependencies yet...)
      months() {
        return _monthLabels.map((ml, i) => ({
          label: ml,
          label_1: ml.substring(0, 1),
          label_2: ml.substring(0, 2),
          label_3: ml.substring(0, 3),
          number: i + 1,
        }));
      },
      // State for weekday header (no dependencies yet...)
      weekdays() {
        return _weekdayLabels.map((wl, i) => ({
          label: wl,
          label_1: wl.substring(0, 1),
          label_2: wl.substring(0, 2),
          label_3: wl.substring(0, 3),
          number: i + 1,
        }));
      },
      // State for calendar header
      header() {
        const month = this.months[this.monthIndex];
        return {
          month,
          year: this.year.toString(),
          shortYear: this.year.toString().substring(2, 4),
          label: month.label,
        };
      },
      // Returns number for first weekday (1-7), starting from Sunday
      firstWeekdayInMonth() {
        return new Date(this.year, this.monthIndex, 1).getDay() + 1;
      },
      // Returns number of days in the current month
      daysInMonth() {
        // Check for February in a leap year
        if (this.month === 2 && this.isLeapYear) return 29;
        // ...Just a normal month
        return _daysInMonths[this.monthIndex];
      },
      weeks() {
        const weeks = [];
        let previousMonth = true; let thisMonth = false; let
          nextMonth = false;
        let day = this.previousMonthComps.days - this.firstWeekdayInMonth + 2;
        let { month } = this.previousMonthComps;
        let { year } = this.previousMonthComps;
        // Cycle through each week of the month, up to 6 total
        for (let w = 1; w <= 6 && !nextMonth; w++) {
          // Cycle through each weekday
          const week = [];
          for (let d = 1; d <= 7; d++) {
            // We need to know when to start counting actual month days
            if (previousMonth && d >= this.firstWeekdayInMonth) {
              // Reset day/month/year counters
              day = 1;
              month = this.month;
              year = this.year;
              // ...and flag we're tracking actual month days
              previousMonth = false;
              thisMonth = true;
            }

            // Append day info for the current week
            // Note: this might or might not be an actual month day
            //  We don't know how the UI wants to display various days,
            //  so we'll supply all the data we can
            week.push({
              label: (day && thisMonth) ? day.toString() : '',
              day,
              weekday: d,
              week: w,
              month,
              year,
              date: new Date(year, month - 1, day),
              beforeMonth: previousMonth,
              afterMonth: nextMonth,
              inMonth: thisMonth,
              isToday: day === _todayComps.day && month === _todayComps.month && year === _todayComps.year,
              isFirstDay: thisMonth && day === 1,
              isLastDay: thisMonth && day === this.daysInMonth,
            });

            // We've hit the last day of the month
            if (thisMonth && day >= this.daysInMonth) {
              thisMonth = false;
              nextMonth = true;
              day = 1;
              month = this.nextMonthComps.month;
              year = this.nextMonthComps.year;
              // Still in the middle of the month (hasn't ended yet)
            } else {
              day++;
            }
          }
          // Append week info for the month
          weeks.push(week);
        }
        return weeks;
      },
    },
    methods: {
      clickOnDay(day) {
        day.isToday = true;

        this.day = day;
        this.$emit('chooseDay', `${this.day} ${this.month} ${this.year}`);
      },
      moveThisMonth() {
        this.month = _todayComps.month;
        this.year = _todayComps.year;
      },
      moveNextMonth() {
        const { month, year } = this.nextMonthComps;
        this.month = month;
        this.year = year;
      },
      movePreviousMonth() {
        const { month, year } = this.previousMonthComps;
        this.month = month;
        this.year = year;
      },
      moveNextYear() {
        this.year++;
      },
      movePreviousYear() {
        this.year--;
      },
    },
  };

  // // JUST NEEDED FOR TUTORIAL - NOT FOR REALSIES
  // const _displayOptions = [
  //   { id: 'label', value: 'label', label: 'Label' },
  //   { id: 'number', value: 'day', label: 'Day' },
  //   { id: 'weekday', value: 'weekday', label: 'Weekday' },
  //   { id: 'week', value: 'week', label: 'Week' },
  //   { id: 'month', value: 'month', label: 'Month' },
  //   { id: 'year', value: 'year', label: 'Year' },
  //   { id: 'beforeMonth', value: 'beforeMonth', label: 'Before Month' },
  //   { id: 'afterMonth', value: 'afterMonth', label: 'After Month' },
  //   { id: 'inMonth', value: 'inMonth', label: 'In Month' },
  //   { id: 'isToday', value: 'isToday', label: 'Is Today' },
  //   { id: 'isFirstDay', value: 'isFirstDay', label: 'Is First Day' },
  //   { id: 'isLastDay', value: 'isLastDay', label: 'Is Last Day' },
  // ];
</script>

<style scoped lang="scss">
  @import '@/styles/_variables.scss';

  $themeColor: #ff7a58;

  $headerPadding: 0.5rem 1rem;
  $headerBorderWidth: 1px;
  $headerBorderStyle: solid;
  $headerBorderColor: #aaaaaa;
  $headerBackground: $themeColor;
  $headerColor: white;

  $weekdayPadding: 0.4rem 0;
  $weekdayColor: #7a7a7a;
  $weekdayBorderWidth: 1px;
  $weekdayBorderStyle: solid;
  $weekdayBorderColor: #aaaaaa;
  $weekdayBackground: #eaeaea;

  $dayColor: #3a3a3a;
  $dayBorder: solid 1px #aaaaaa;
  $dayBackgroundColor: white;
  $dayWidth: 14.2857%;
  $dayHeight: 50px;

  $todayColor: white;
  $todayBackgroundColor: $themeColor;

  $fontFamily: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", "Helvetica", "Arial", sans-serif;

  * {
    box-sizing: border-box;
  }


  #app {
    font-family: $fontFamily;
    padding: 20px;
  }


  .calendar {
    display: flex;
    flex-direction: column;
    width: 95%;
    margin: 43px 0 25px;
    @media (max-width: 1300px) {
      width: 100%;
    }
  }


  .header {
    display: flex;
    justify-content: stretch;
    align-items: center;
    color: $headerColor;
   padding-bottom: 5px;
    border-width: $headerBorderWidth;
    width: 93%;
    margin: 0 auto;
  }

  .arrow {
    font-size: 1.8rem;
    font-weight: 500;
    user-select: none;
    flex-grow: 0;
  }


  .title {
    /*+pointer*/
    flex-grow: 1;
    font-size: 1.2rem;
    text-align: center;
    color: $med-blue;
  }


  .weekdays {
    display: flex;
    flex: auto;
  }


  .weekday {
    width: $dayWidth;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: $weekdayPadding;
    color: $white;
    opacity: 0.5;

  }


  .week {
    display: flex;
  }


  .day {
    width: $dayWidth;
    height: 45px;
    display: flex;
    justify-content: center;
    align-items: center;

  }


  .today {
    font-weight: 500;
    color: $yellow;
  }

  .year {
    text-align: center;
    color: $med-blue;
    font-weight: bold;
    display: flex;
    align-items: center;
    justify-content: center;
    &:after {
      content: "";
      width: 110px;
      height: 1px;
      background-color: $med-blue;
      display: block;
      margin-left: 10px;
    }
    &:before {
      content: "";
      width: 110px;
      height: 1px;
      background-color: $med-blue;
      display: block;
      margin-right: 10px;
    }
  }

</style>
