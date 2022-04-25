<template>
  <div class="thuong-popup">
    <div class="overlay-popup" @click="closePopup"></div>
    <div class="thuong-popup__container">
      <div class="thuong-popup__header">
        <div class="year-click" :class="{ 'text-white': mode == 'chooseYear' }">
          <span @click="mode = 'chooseYear'" v-if="from_date || to_date">
            {{ from_date ? getChooseMonth(from_date) : "?" }} -
            {{ to_date ? getChooseMonth(to_date) : "?" }}
          </span>
          <span @click="mode = 'chooseYear'" v-else>-</span>

          <button @click="getToday">
            <svg
              class="svg-icon"
              style="vertical-align: middle; overflow: hidden"
              viewBox="0 0 1024 1024"
              version="1.1"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M832 128h-64V64h-64v64H320V64h-64v64h-64a64.19 64.19 0 0 0-64 64v640a64.19 64.19 0 0 0 64 64h640a64.19 64.19 0 0 0 64-64V192a64.19 64.19 0 0 0-64-64z m0 704H192V256h640z"
              />
              <path d="M256 320h224v192H256z" />
            </svg>
            <span class="tooltiptext">Now</span>
          </button>
        </div>
        <div class="date-month-click">
          {{ getChooseDate }}
        </div>
      </div>
      <div class="thuong-popup__body" v-if="mode == 'chooseDate'">
        <div class="thuong-popup__body-top">
          <div class="month-picker">
            <span class="btn-prev" @click="decreaseMonth">&#10094;</span>
            <span @click="mode = 'chooseMonth'">{{ monthChoose.long_nm }}</span>
            <span class="btn-next" @click="increaseMonth">&#10095;</span>
          </div>

          <div class="year-picker">
            <span class="btn-prev" @click="decreaseYear">&#10094;</span>
            <span @click="mode = 'chooseYear'">{{ yearChoose }}</span>
            <span class="btn-next" @click="increaseYear">&#10095;</span>
          </div>
        </div>

        <table>
          <thead>
            <tr>
              <th v-for="weekday in weekdays" :key="weekday.key">
                {{ weekday.short_nm }}
              </th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="n in (1, numberOfWeek)" :key="n">
              <td
                v-for="weekday in weekdays"
                :key="weekday.key"
                :class="{
                  active: checkActiveDate(
                    yearChoose,
                    monthChoose,
                    filterDateByGroup(n - 1)[weekday.key]
                  ),
                  'in-range':
                    checkInRangeDate(
                      yearChoose,
                      monthChoose.key - 1,
                      filterDateByGroup(n - 1)[weekday.key]
                    ) && from_date != to_date,
                  first:
                    checkFromDate(
                      yearChoose,
                      monthChoose.key - 1,
                      filterDateByGroup(n - 1)[weekday.key]
                    ) && from_date != to_date,
                  last:
                    checkToDate(
                      yearChoose,
                      monthChoose.key - 1,
                      filterDateByGroup(n - 1)[weekday.key]
                    ) && from_date != to_date,
                  'in-range-hover': checkInRangeDateHover(
                    yearChoose,
                    monthChoose.key - 1,
                    filterDateByGroup(n - 1)[weekday.key]
                  ),
                  'first-range':
                    checkFromDateTemp(
                      yearChoose,
                      monthChoose.key - 1,
                      filterDateByGroup(n - 1)[weekday.key]
                    ) && !checkReverseRange,
                  'last-range':
                    checkToDateTemp(
                      yearChoose,
                      monthChoose.key - 1,
                      filterDateByGroup(n - 1)[weekday.key]
                    ) && !checkReverseRange,
                  'from-last-range':
                    checkFromDateTemp(
                      yearChoose,
                      monthChoose.key - 1,
                      filterDateByGroup(n - 1)[weekday.key]
                    ) && checkReverseRange,
                  'to-first-range':
                    checkToDateTemp(
                      yearChoose,
                      monthChoose.key - 1,
                      filterDateByGroup(n - 1)[weekday.key]
                    ) && checkReverseRange,
                  'in-reverse-range-hover':
                    checkReverseRange &&
                    checkReverseInRange(
                      yearChoose,
                      monthChoose.key - 1,
                      filterDateByGroup(n - 1)[weekday.key]
                    ),
                }"
              >
                <div class="text-center">
                  <span
                    v-if="
                      filterDateByGroup(n - 1)[weekday.key] &&
                      filterDateByGroup(n - 1)[weekday.key] != 0
                    "
                    @click="
                      chooseDate(
                        yearChoose,
                        monthChoose,
                        filterDateByGroup(n - 1)[weekday.key]
                      )
                    "
                    @mouseover="
                      hoverDate(
                        yearChoose,
                        monthChoose,
                        filterDateByGroup(n - 1)[weekday.key]
                      )
                    "
                    @mouseleave="clearToDateTemp"
                  >
                    {{ filterDateByGroup(n - 1)[weekday.key] }}
                  </span>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="thuong-popup__body" v-if="mode == 'chooseYear'">
        <div class="choose-container">
          <div class="btn-prev" @click="decreaseYearRange">&#10094;</div>
          <div class="choose-items">
            <div class="choose-item" v-for="item in years" :key="item">
              <div
                class="item"
                :class="{ active: item == year }"
                @click="chooseYear(item)"
              >
                {{ item }}
              </div>
            </div>
          </div>
          <div class="btn-next" @click="increaseYearRange">&#10095;</div>
        </div>
      </div>

      <div class="thuong-popup__body" v-if="mode == 'chooseMonth'">
        <div class="choose-container">
          <div class="choose-items">
            <div class="choose-item" v-for="item in months" :key="item.key">
              <div
                class="item"
                :class="{ active: item == month }"
                @click="chooseMonth(item)"
              >
                {{ item.short_nm }}
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="thuong-popup__footer">
        <button @click="clear">Clear</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["type", "locale", "value"],
  data() {
    return {
      monthChoose: null,
      months: [],
      yearChoose: null,
      years: [],
      weekday: null,
      weekdays: [],
      dates: [],
      dateByGroup: [],
      chooseDay: null,
      startDay: null,
      numberOfFirstWeek: 0,
      numberOfWeek: 0,
      mode: "chooseDate",
      rangeMode: false,
      from_date: null,
      to_date: null,
      from_date_temp: null,
      to_date_temp: null,
    };
  },
  created() {
    this.getMonthList();
    this.getWeekDays();
    let now = new Date();
    if (typeof this.value == "string") {
      this.$emit("input", null);
    }
    if (
      this.value &&
      this.value.from_date &&
      this.value.to_date &&
      this.value.from_date != "" &&
      this.value.to_date != ""
    ) {
      this.from_date = this.value.from_date;
      this.to_date = this.value.to_date;
    }
    const weekday = now.toLocaleDateString(this.locale, { weekday: "short" });
    this.weekday = this.weekdays.find((c) => c.short_nm == weekday);
    this.monthChoose = this.months.find((c) => c.key == now.getMonth() + 1);
    this.yearChoose = now.getFullYear();
    this.getListYear(now.getFullYear());
    this.prepareDates();
  },
  computed: {
    getChooseMonth() {
      return (dateTime) => {
        const newDate = new Date(dateTime);
        const dateOptions = {
          month: "short",
          year: "numeric",
        };
        return newDate.toLocaleDateString(this.locale, dateOptions);
      };
    },
    getChooseDate() {
      return this.from_date && this.to_date
        ? this.from_date + " - " + this.to_date
        : "--";
    },
    convertDate() {
      return (year, month, date) => {
        let Month = month ? month.key - 1 : 1;
        const newDate = new Date(year, Month, date);
        const dateOptions = {
          year: "numeric",
          month: "2-digit",
          day: "2-digit",
        };
        return newDate.toLocaleString(this.locale, dateOptions);
      };
    },
    filterDateByGroup() {
      return (group) => {
        return this.dateByGroup.find((c) => c.key == group)
          ? this.dateByGroup.find((c) => c.key == group).date
          : [];
      };
    },
    checkInRangeDate() {
      return (year, month, date) => {
        let timeChoose = new Date(year, month, date).getTime();
        let fromTime = new Date(this.from_date).getTime();
        let toTime = new Date(this.to_date).getTime();
        return fromTime <= timeChoose && timeChoose <= toTime;
      };
    },
    checkActiveDate() {
      return (year, month, date) => {
        return (
          this.convertDate(year, month, date) == this.from_date ||
          this.convertDate(year, month, date) == this.to_date
        );
      };
    },
    checkFromDate() {
      return (year, month, date) => {
        let timeChoose = new Date(year, month, date).getTime();
        let fromTime = new Date(this.from_date).getTime();
        return fromTime == timeChoose;
      };
    },
    checkToDate() {
      return (year, month, date) => {
        let timeChoose = new Date(year, month, date).getTime();
        let toTime = new Date(this.to_date).getTime();
        return toTime == timeChoose;
      };
    },
    checkInRangeDateHover() {
      return (year, month, date) => {
        let timeChoose = new Date(year, month, date).getTime();
        let fromTime = new Date(this.from_date_temp).getTime();
        let toTime = new Date(this.to_date_temp).getTime();
        return fromTime <= timeChoose && timeChoose <= toTime;
      };
    },
    checkFromDateTemp() {
      return (year, month, date) => {
        let timeChoose = new Date(year, month, date).getTime();
        let fromTime = new Date(this.from_date_temp).getTime();
        return fromTime == timeChoose;
      };
    },
    checkToDateTemp() {
      return (year, month, date) => {
        let timeChoose = new Date(year, month, date).getTime();
        let toTime = new Date(this.to_date_temp).getTime();
        return toTime == timeChoose;
      };
    },
    getValue() {
      return {
        from_date: this.from_date,
        to_date: this.to_date,
      };
    },
    checkReverseRange() {
      return (
        new Date(this.to_date_temp).getTime() <
        new Date(this.from_date_temp).getTime()
      );
    },
    checkReverseInRange() {
      return (year, month, date) => {
        let timeChoose = new Date(year, month, date).getTime();
        let fromTime = new Date(this.from_date_temp).getTime();
        let toTime = new Date(this.to_date_temp).getTime();
        return toTime > 0 && fromTime >= timeChoose && timeChoose >= toTime;
      };
    },
  },
  watch: {
    getValue: function (value) {
      if (value) {
        this.$emit("input", value);
      }
    },
  },
  methods: {
    clear() {
      this.from_date = null;
      this.to_date = null;
      this.$emit("onClear");
    },
    getMonthList() {
      this.months = [];
      for (let i = 0; i < 12; i++) {
        let data = {
          key: i + 1,
          short_nm: new Date(null, i + 1, null).toLocaleDateString(
            this.locale,
            {
              month: "short",
            }
          ),
          long_nm: new Date(null, i + 1, null).toLocaleDateString(this.locale, {
            month: "long",
          }),
        };
        this.months.push(data);
      }
    },
    getListYear(from) {
      this.years = [];
      for (let j = 1; j < 9; j++) {
        this.years.push(from - j);
      }

      this.years.reverse();

      for (let i = 0; i < 10; i++) {
        this.years.push(from + i);
      }
    },
    getWeekDays() {
      let baseDate = new Date(Date.UTC(2017, 0, 2));
      this.weekdays = [];
      for (let i = 0; i < 7; i++) {
        let data = {
          key: i,
          short_nm: baseDate.toLocaleDateString(this.locale, {
            weekday: "short",
          }),
          long_nm: baseDate.toLocaleDateString(this.locale, {
            weekday: "long",
          }),
        };
        this.weekdays.push(data);
        baseDate.setDate(baseDate.getDate() + 1);
      }
    },
    getAllDaysInMonth(year, month) {
      const date = new Date(year, month, 1);
      const dates = [];
      while (date.getMonth() === month) {
        dates.push(new Date(date).getDate());
        date.setDate(date.getDate() + 1);
      }
      return dates;
    },
    prepareDates() {
      this.dates = this.getAllDaysInMonth(
        this.yearChoose,
        this.monthChoose.key - 1
      );

      this.startDay = new Date(this.yearChoose, this.monthChoose.key - 1, 1);

      let firstWeek = this.startDay.toLocaleDateString(this.locale, {
        weekday: "short",
      });

      this.numberOfWeek = Math.ceil(this.dates.length / 7) + 1;

      this.dateByGroup = [];

      this.numberOfFirstWeek =
        7 - this.weekdays.find((c) => c.short_nm == firstWeek).key;

      let firstDates = [];
      for (let i = 0; i < this.numberOfFirstWeek; i++) {
        firstDates.push(this.dates[i]);
      }
      if (this.numberOfFirstWeek < 7) {
        for (let k = 0; k < 7 - this.numberOfFirstWeek; k++) {
          firstDates.unshift(0);
        }
      }
      let firstData = {
        key: 0,
        date: firstDates,
      };
      this.dateByGroup.push(firstData);

      for (let i = 0; i < this.numberOfWeek - 1; i++) {
        let dates = [];
        for (
          let j = this.numberOfFirstWeek + 7 * i;
          j < this.numberOfFirstWeek + 7 * i + 7;
          j++
        ) {
          dates.push(this.dates[j]);
        }
        let data = {
          key: i + 1,
          date: dates,
        };

        this.dateByGroup.push(data);
      }
    },
    closePopup() {
      this.$emit("closePopup", false);
    },
    chooseDate(year, month, date) {
      this.to_date_temp = null;
      if (this.from_date && this.to_date) {
        this.from_date = null;
        this.to_date = null;
        this.rangeMode = false;
      } else {
        if (!this.rangeMode) {
          this.from_date_temp = this.convertDate(year, month, date);
        } else {
          if (
            new Date(this.from_date_temp).getTime() >
            new Date(year, month.key - 1, date, 0, 0, 0).getTime()
          ) {
            this.to_date = this.from_date_temp;
            this.from_date = this.convertDate(year, month, date);
          } else {
            this.from_date = this.from_date_temp;
            this.to_date = this.convertDate(year, month, date);
          }
          this.from_date_temp = null;
          this.$emit("onSelect", this.getValue);
        }
        this.rangeMode = !this.rangeMode;
      }
    },
    decreaseMonth() {
      if (this.monthChoose.key == 1) {
        this.yearChoose = this.yearChoose - 1;
        this.monthChoose = this.months.find((c) => c.key == 12);
      } else {
        this.monthChoose = this.months.find(
          (c) => c.key == this.monthChoose.key - 1
        );
      }
      this.prepareDates();
    },
    increaseMonth() {
      if (this.monthChoose.key == 12) {
        this.yearChoose = this.yearChoose + 1;
        this.monthChoose = this.months.find((c) => c.key == 1);
      } else {
        this.monthChoose = this.months.find(
          (c) => c.key == this.monthChoose.key + 1
        );
      }
      this.prepareDates();
    },
    decreaseYear() {
      this.yearChoose -= 1;
      this.prepareDates();
    },
    increaseYear() {
      this.yearChoose += 1;
      this.prepareDates();
    },
    decreaseYearRange() {
      let from = this.years[0] - 10;
      this.getListYear(from);
    },
    increaseYearRange() {
      let from = this.years.slice(-1).pop() + 9;
      this.getListYear(from);
    },
    chooseYear(year) {
      this.yearChoose = year;
      this.mode = "chooseDate";
    },
    chooseMonth(month) {
      this.monthChoose = month;
      this.mode = "chooseDate";
    },
    getToday() {
      const now = new Date();
      this.yearChoose = now.getFullYear();
      this.monthChoose = this.months.find((c) => c.key == now.getMonth() + 1);
      this.prepareDates();
    },
    hoverDate(year, month, date) {
      if (this.from_date_temp) {
        this.to_date_temp = this.convertDate(year, month, date);
      }
    },
    clearToDateTemp() {
      this.to_date_temp = null;
    },
  },
};
</script>

<style lang="scss" scoped>
.text-white {
  color: #fff !important;
}
.ml-sm {
  margin-left: 8px;
}
.thuong-popup {
  position: absolute;
  top: 42px;
  left: 0;
  z-index: 9;

  .overlay-popup {
    position: fixed;
    content: "";
    z-index: -1;
    width: 100vw;
    height: 100vh;
    background: transparent;
    top: 0;
    left: 0;
  }

  &__container {
    background: #fff;
    border-radius: 4px;
    min-width: 290px;
    border: 1px solid #dcdcdc;
    box-shadow: 0 1px 5px #0003, 0 2px 2px #00000024, 0 3px 1px -2px #0000001f;
    z-index: 1;
  }

  &__header {
    padding: 16px;
    background: #16a085;

    .year-click {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-weight: 700;
      color: #f0f0f0;
      text-align: left;
      margin-bottom: 8px;
      transition: all 0.25s;

      span {
        cursor: pointer;
      }

      button {
        background: transparent;
        border: none;
        outline: 0;
        cursor: pointer;
        transition: all 0.25s;
        padding: 4px;
        border-radius: 4px;
        position: relative;

        svg {
          width: 18px;
          height: 18px;
          fill: #fff;
        }

        .tooltiptext {
          visibility: hidden;
          background-color: black;
          color: #fff;
          text-align: center;
          border-radius: 4px;
          padding: 4px 12px;

          /* Position the tooltip */
          position: absolute;
          top: -29px;
          left: -12px;
          z-index: 2;
        }

        &:hover {
          background: rgba($color: #f0f0f0, $alpha: 0.2);

          .tooltiptext {
            visibility: visible;
          }
        }
      }

      &:hover {
        color: #fff;
      }
    }

    .date-month-click {
      font-size: 18px;
      font-weight: 700;
      color: #f0f0f0;
      text-align: left;
    }
  }

  &__body {
    padding: 16px;

    &-top {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 8px;

      .month-picker {
        width: 58%;
        display: flex;
        justify-content: space-between;
        align-items: center;

        span {
          padding: 2px 4px;
          cursor: pointer;
          transition: all 0.25s ease-in-out;
          font-size: 14px;

          &:hover {
            background: #f0f0f0;
          }
        }
      }

      .year-picker {
        width: 38%;
        display: flex;
        justify-content: space-between;
        align-items: center;

        span {
          padding: 2px 4px;
          cursor: pointer;
          transition: all 0.25s ease-in-out;
          font-size: 14px;

          &:hover {
            background: #f0f0f0;
          }
        }
      }
    }

    table {
      width: 100%;
      border-collapse: collapse;

      thead {
        tr {
          th {
            color: #aaa;
            font-weight: 400;
            font-size: 14px;
            padding: 8px 4px;
          }
        }
      }

      tbody {
        tr {
          td {
            height: 33px;
            width: 33px;
            padding: 2px 0;

            .text-center {
              text-align: center;
              position: relative;

              z-index: 1;

              span {
                display: inline-flex;
                justify-content: center;
                align-items: center;
                font-size: 14px;
                cursor: pointer;
                border-radius: 50%;
                width: 28px;
                height: 28px;
                transition: all 0.25s ease-in-out;
                z-index: 2;
              }
            }

            &:hover span {
              background: rgb(22, 160, 133, 0.1);
            }

            &.active {
              span {
                background: #16a085;
                color: #fff;
              }
            }

            &.in-range {
              .text-center::before {
                background: rgb(22, 160, 132, 0.3);
                position: absolute;
                top: 0;
                left: 0;
                content: "";
                width: 100%;
                height: 100%;
                z-index: -1;
              }
            }

            &.first {
              .text-center::before {
                left: 50%;
                width: 50%;
              }
            }

            &.last {
              .text-center::before {
                right: 50%;
                width: 50%;
              }
            }

            &.first-range,
            &.to-first-range {
              .text-center::after {
                background: transparent;
                border-top: 1px dashed blue;
                border-left: 1px dashed blue;
                border-bottom: 1px dashed blue;
                position: absolute;
                top: -6%;
                left: 0;
                content: "";
                border-radius: 4px 0 0 4px;
                width: 100%;
                height: 110%;
                z-index: -1;
                transition: all 0.25s;
              }
            }

            &.last-range,
            &.from-last-range {
              .text-center::after {
                background: transparent;
                border-top: 1px dashed blue;
                border-right: 1px dashed blue;
                border-bottom: 1px dashed blue;
                position: absolute;
                top: -6%;
                left: 0;
                content: "";
                border-radius: 0 4px 4px 0;
                width: 100%;
                height: 110%;
                z-index: -1;
                transition: all 0.25s;
              }
            }

            &.in-range-hover,
            &.in-reverse-range-hover {
              .text-center::after {
                background: transparent;
                border-top: 1px dashed blue;
                border-bottom: 1px dashed blue;
                position: absolute;
                top: -6%;
                left: 0;
                content: "";
                width: 100%;
                height: 110%;
                z-index: -1;
                transition: all 0.25s;
              }
            }
          }
        }
      }
    }

    .choose-container {
      display: flex;
      align-items: center;
      justify-content: space-between;

      .btn-prev,
      .btn-next {
        display: inline-flex;
        justify-content: center;
        align-items: center;
        width: 24px;
        height: 24px;
        cursor: pointer;
        border-radius: 50%;
        transition: all 0.25s;

        &:hover {
          background: #f0f0f0;
        }
      }

      .choose-items {
        width: 100%;
        display: flex;
        flex-wrap: wrap;

        .choose-item {
          width: 33%;
          padding: 0 8px;
          box-sizing: border-box;
          margin-bottom: 5px;
          cursor: pointer;
          text-align: center;

          .item {
            border-radius: 4px;
            transition: all 0.25s;
            padding: 8px 4px;
            font-size: 14px;

            &:hover {
              background: #f0f0f0;
            }

            &.active {
              background: #16a085;
              color: #fff;
            }
          }
        }
      }
    }
  }

  &__footer {
    padding: 8px 16px;
    display: flex;
    justify-content: flex-end;

    button {
      background: transparent;
      cursor: pointer;
      padding: 5px 12px;
      color: #16a085;
      border: none;
      outline: 0;
      border-radius: 4px;
      transition: all 0.25s;

      &:hover {
        background: rgb(22, 160, 133, 0.1);
      }
    }
  }
}
</style>
