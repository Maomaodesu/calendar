<template>
  <div class="main_button">
    <div class="main_b_box">
      <div class="main_scroll">
        <div class="scroll">
          <h1>Calendar 日历</h1>
          <h2>概述</h2>
          <p>自定义日历</p >
          <h2>基础用法</h2>
          <div class="card">
            <div class="plan-zone">
              <div class="btn-group">
                <div class="left-btn">
                  <star-button type="primary" @click="prevMonth">上一个月</star-button>
                  <star-button type="primary" @click="goToCurrentMonth">{{ getCurDate() }}</star-button>
                  <star-button type="primary" @click="nextMonth">下一个月</star-button>
                  <star-button type="primary" @click="goToCurrentDay">回到今天</star-button>
                </div>
                <div class="right-btn">
                  <button class="new">新安排</button>
                  <button class="ing">进行中</button>
                  <button class="finish">已完成</button>
                </div>
              </div>
              <!-- <el-calendar v-model="value"> </el-calendar> -->
              <table class="parent-table">
                <thead>
                  <th>周一</th>
                  <th>周二</th>
                  <th>周三</th>
                  <th>周四</th>
                  <th>周五</th>
                  <th>周六</th>
                  <th>周日</th>
                </thead>
                <tbody>
                  <tr v-for="(week, windex) in weeks" :key="windex">
                    <td v-for="(day, dindex) in week" :class="{ highlight: isToday(day.date) }" :key="dindex">
                      <div class="content" :class="{ faded: !isCurrentMonth(day.date) }" @click="handleDay(day)">
                        <div class="top-day">{{ day.date }}日</div>
                        <div class="middle-event" v-if="day.remarkList && day.remarkList.length > 0">
                          <div v-for="(item, index) in day.remarkList" :key="index" :class="['remark', { 'red': item.type === 1, 'blue': item.type === 2}]">{{ item.title }}</div>
                        </div>
                      </div>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>

export default {
  name: 'HomeCalendar',
  components: {},
  data() {
    return {
      // 后台返回的数据
      dataList: [
        { date: '2025-01-28', remarkList: [{ id: 1, title: '我是第一行', type: 1 }, { id: 2, title: '我是第二行', type: 2 }] },
        { date: '2025-02-01', remarkList: [{ id: 1, title: '我是第一行', type: 1 }] },
        { date: '2025-02-02', remarkList: [{ id: 1, title: '我是第一行', type: 1 }, { id: 2, title: '我是第二行', type: 2 }] },
      ],
      current: new Date(),
      today: new Date(),
    }
  },
  computed: {
    weeks() {
      return this.getMonthData(
        this.current.getFullYear(),
        this.current.getMonth() + 1
      );
    },
  },
  mounted() {},
  methods: {
    handleDay(v) {
      console.log('v', v)
    },
    getCurDate() {
      var date = new Date();
      var year = date.getFullYear();
      var month = date.getMonth() + 1; // getMonth() returns a zero-based value (0-11)
      if (month < 10) {
        month = "0" + month; // add a leading zero if the month is a single digit
      }
      return year + "-" + month;
    },
    isToday(date) {
      const time = new Date(date)
      let today = new Date();
      return (
        time.getDate() === today.getDate() &&
        time.getMonth() === today.getMonth() &&
        time.getFullYear() === today.getFullYear()
      );
    },
    goToCurrentDay() {
      this.current = new Date(this.today);
    },
    isCurrentMonth(date) {
      const time = new Date(date)
      return time.getMonth() === this.current.getMonth();
    },
    prevMonth() {
      this.current.setMonth(this.current.getMonth() - 1);
      this.current = new Date(this.current);
      // console.log(this.current.getFullYear(), 'dedede')
    },
    nextMonth() {
      this.current.setMonth(this.current.getMonth() + 1);
      this.current = new Date(this.current);
    },
    goToCurrentMonth() {
      this.current = new Date(this.today);
    },
    getMonthData(year, month) {
      let weeks = [];
      let firstDay = new Date(year, month - 1, 1); // 这个月的第一天
      let lastDayOfCurrentMonth = new Date(year, month, 0); // 这个月的最后一天
      let lastDayOfPrevMonth = new Date(year, month - 1, 0); // 上个月的最后一天

      //这里的0有一个特殊的意义，它可以返回上个月的最后一天。也就是说，如果你想知道某个月有多少天，你可以创建一个日期对象，年份和月份设置为你想知道的月份，日期设置为0，然后调用getDate()方法，返回的就是那个月的天数。
      // new Date(year, month - 1, 0) 最后一个参数，超过当月天数重新排序，比如1月31天，最后一个参数为33返回2
      let startDayOfWeek = firstDay.getDay() === 0 ? 7 : firstDay.getDay(); // 开始是周几
      let dayCount = 1; // 当前日期的变量，初始值为1
      // 上一个月的天数
      let prevMonthDayCount = lastDayOfPrevMonth.getDate() - startDayOfWeek + 2; // 这是为了在日历中填充上个月的日期。
      // console.log(lastDayOfPrevMonth.getDate(), startDayOfWeek, prevMonthDayCount)
      for (let i = 0; i < 6; i++) {
        let week = [];
        for (let j = 0; j < 7; j++) {
         // 日期为上个月的日期，然后将这个日期对象添加到`week`数组中，同时`prevMonthDayCount`加1。
          if (i === 0 && j < startDayOfWeek - 1) {

            week.push({ date: this.handleTime(new Date(year, month - 2, prevMonthDayCount++)) });
            // 日期为下个月的日期，然后将这个日期对象添加到`week`数组中，同时`dayCount`加1
          } else if (dayCount > lastDayOfCurrentMonth.getDate()) {
            week.push({
              date: this.handleTime(new Date( year, month, dayCount++ - lastDayOfCurrentMonth.getDate()))
            });
           // 日期为这个月的日期，然后将这个日期对象添加到`week`数组中，同时`dayCount`加1
          } else {
            week.push({ date: this.handleTime(new Date(year, month - 1, dayCount++)) });
          }
        }
        weeks.push(this.handleData(week));
        console.log('matchingData', weeks)
      }
      return weeks;
    },
    handleData(list) {
      const dataList = this.dataList
      const mergedData = list.reduce((acc, current) => {
        const matchingData = dataList.find(item => item.date === current.date);
        if (matchingData) {
          acc.push({ ...current, remarkList: matchingData.remarkList })
        } else {
          acc.push(current)
        }
        return acc
      }, [])
      return mergedData
    },
    handleTime(v) {
      const time = v;
      const year = time.getFullYear();
      const month = (time.getMonth() + 1).toString().padStart(2, '0');
      const day = time.getDate().toString().padStart(2, '0');
      const formattedDate = `${year}-${month}-${day}`;
      return formattedDate
    },
  }
}
</script>

<style lang="scss" scoped>
.main_button {
  width: 100%;
  height: 100%;
  padding: 12px;
  box-sizing: border-box;

  .main_b_box {
    height: 100%;
    background: #fff;
    overflow: hidden;

    .main_scroll {
      overflow-y: auto;
      height: 100%;

      .scroll {
        box-sizing: border-box;
        padding: 32px;

        h1 {
          color: rgba(0, 0, 0, 0.85);
          font-size: 30px;
          font-weight: 500;
          margin: 0 0 10px 0;
        }

        h2 {
          color: #515a6e;
          font-size: 24px;
          font-weight: 400;
          margin: 0 0 10px 0;
        }

        p {
          font-size: 14px;
          color: #515a6e;
        }

        .card {
          border: 1px solid #dcdee2;
          border-color: #e8eaec;
          width: 100%;
          margin-top: 20px;
          border-radius: 4px;
          padding: 16px;
          box-sizing: border-box;
          box-shadow: 0 8px 12px #ebedf0;
        }
      }
    }

    .main_scroll::-webkit-scrollbar {
      display: none;
    }
  }
}
.faded {
  opacity: 0.3;
}
.highlight {
  background: rgba(255, 220, 40, 0.15);
}
.plan-zone {
  margin-top: 10px;
  .btn-group {
    display: flex;
    justify-content: space-between;
    .right-btn {
      button.new {
        background-color: #fff;
        border: 1px solid #fff;
        color: #409eef;
        position: relative;
        &::before {
          content: "";
          width: 8px;
          height: 8px;
          border-radius: 50%;
          position: absolute;
          top: 7px;
          left: -3px;
          background-color: #409eef;
        }
      }
      button.ing {
        background-color: #fff;
        border: 1px solid #fff;
        color: #ff974a;
        position: relative;
        &::before {
          content: "";
          width: 8px;
          height: 8px;
          border-radius: 50%;
          position: absolute;
          top: 7px;
          left: -3px;
          background-color: #ff974a;
        }
      }
      button.finish {
        background-color: #fff;
        border: 1px solid #fff;
        color: #3dd599;
        position: relative;
        &::before {
          content: "";
          width: 8px;
          height: 8px;
          border-radius: 50%;
          position: absolute;
          top: 7px;
          left: -3px;
          background-color: #3dd599;
        }
      }
    }
  }
}
.parent-table {
  border-collapse: collapse;
  table-layout: fixed;
  width: 100%;
  margin-top: 20px;
  th,
  td {
    width: 14.4%;
    border: 1px solid #ddd;
  }
  td {
    padding: 2px 3px;

    .content {
      position: relative;
      min-height: 80px;
    }
    vertical-align: top;
    .top-day {
      text-align: right;
      font-size: 13px;
    }
  }
}
.table-date {
  display: flex;
  > div {
    flex: 1;
  }
}
.red{
  color: red;
}
.blue{
  color: blue;
}
</style>