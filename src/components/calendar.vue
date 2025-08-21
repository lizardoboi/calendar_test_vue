<template>
  <div class="calendar">
    <div class="calendar-header">
      <button @click="prevMonth">‹</button>
      <span>{{ monthName }} {{ currentYear }}</span>
      <button @click="nextMonth">›</button>
    </div>

    <div class="calendar-weekdays">
      <span v-for="(day, i) in weekdays" :key="i">{{ day }}</span>
    </div>

    <div class="calendar-days">
      <span
          v-for="(cell, idx) in grid"
          :key="idx"
          class="calendar-day"
          :class="{
          'other-month': cell.otherMonth,
          'selected': isSelected(cell.date)
        }"
          @click="selectDate(cell.date)"
      >
        {{ cell.date.getDate() }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: "Calendar",
  props: {
    value: { type: String, default: null },
    locale: { type: String, default: "en" }
  },
  data() {
    const initial = this.value ? this.parseDate(this.value) : new Date();
    return {
      selectedDate: initial,
      currentYear: initial.getFullYear(),
      currentMonth: initial.getMonth()
    };
  },
  computed: {
    dictionary() {
      return {
        en: {
          months: [
            "Jan","Feb","Mar","Apr","May","Jun",
            "Jul","Aug","Sep","Oct","Nov","Dec"
          ],
          weekdays: ["Sun","Mon","Tue","Wed","Thu","Fri","Sat"]
        },
        ru: {
          months: [
            "Янв","Фев","Мар","Апр","Май","Июн",
            "Июл","Авг","Сен","Окт","Ноя","Дек"
          ],
          weekdays: ["Вс","Пн","Вт","Ср","Чт","Пт","Сб"]
        }
      }[this.locale] || this.dictionary.en;
    },
    monthName() {
      return this.dictionary.months[this.currentMonth];
    },
    weekdays() {
      return this.dictionary.weekdays;
    },
    grid() {
      const firstOfMonth = new Date(this.currentYear, this.currentMonth, 1);
      const daysInMonth = new Date(this.currentYear, this.currentMonth + 1, 0).getDate();

      const startOffset = firstOfMonth.getDay();
      const cells = [];

      for (let i = startOffset; i > 0; i--) {
        const d = new Date(this.currentYear, this.currentMonth, 1 - i);
        cells.push({ date: d, otherMonth: true });
      }

      for (let d = 1; d <= daysInMonth; d++) {
        cells.push({ date: new Date(this.currentYear, this.currentMonth, d), otherMonth: false });
      }

      const trailing = (7 - (cells.length % 7)) % 7;
      for (let i = 1; i <= trailing; i++) {
        const d = new Date(this.currentYear, this.currentMonth, daysInMonth + i);
        cells.push({ date: d, otherMonth: true });
      }

      return cells;
    }
  },
  methods: {
    prevMonth() {
      if (this.currentMonth === 0) {
        this.currentMonth = 11;
        this.currentYear--;
      } else {
        this.currentMonth--;
      }
    },
    nextMonth() {
      if (this.currentMonth === 11) {
        this.currentMonth = 0;
        this.currentYear++;
      } else {
        this.currentMonth++;
      }
    },
    selectDate(date) {
      this.selectedDate = new Date(date);
      const iso = this.formatDate(this.selectedDate);
      this.$emit("select", iso);
      this.$emit("input", iso);
    },
    isSelected(date) {
      const s = this.selectedDate;
      return (
          s &&
          date.getDate() === s.getDate() &&
          date.getMonth() === s.getMonth() &&
          date.getFullYear() === s.getFullYear()
      );
    },
    parseDate(str) {
      const m = /^([0-9]{4})-([0-9]{2})-([0-9]{2})$/.exec(str);
      if (m) {
        return new Date(+m[1], +m[2] - 1, +m[3]);
      }
      return new Date(str);
    },
    formatDate(date) {
      const y = date.getFullYear();
      const m = String(date.getMonth() + 1).padStart(2, "0");
      const d = String(date.getDate()).padStart(2, "0");
      return `${y}-${m}-${d}`;
    }
  }
};
</script>

<style scoped>
.calendar {
  width: 260px;
  font-family: Arial, sans-serif;
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 4px;
}

.calendar-header button {
  background: none;
  border: none;
  font-size: 18px;
  cursor: pointer;
}

.calendar-header span {
  font-weight: bold;
}

.calendar-weekdays,
.calendar-days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  text-align: center;
}

.calendar-weekdays span {
  font-size: 12px;
  color: #555;
  padding: 4px 0;
}

.calendar-day {
  padding: 6px 0;
  cursor: pointer;
  border: 1px solid transparent;
}

.calendar-day.other-month {
  color: #aaa;
}

.calendar-day.selected {
  border: 1px solid #000;
}

.calendar-day:hover {
  background: #eee;
}
</style>