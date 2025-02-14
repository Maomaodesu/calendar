<template>
  <div>
    <h1>Demo App</h1>
    <FullCalendar :options='calendarOptions'>
      <template v-slot:eventContent='arg'>
        <b>{{ arg.timeText }}</b>
        <i>{{ arg.event.title }}</i>
      </template>
    </FullCalendar>
  </div>
</template>


<script>  
  import FullCalendar from '@fullcalendar/vue'  
  import dayGridPlugin from '@fullcalendar/daygrid'  
  import timeGridPlugin from '@fullcalendar/timegrid'  
  import axios from 'axios';
  import '@/styles/index.scss' // global css
  const scanPath = `${process.env.VUE_APP_BASE_API}/calendar/calendar`; // 构造请求路径
  export default {
    components: {
      FullCalendar // make the <FullCalendar> tag available
    },
    data() {
      return {
        startDate: '2025-02-01',
        areaId: 1,
        rawData: [],
        calendarData: [],
        calendarOptions: {
          plugins: [dayGridPlugin],
          initialView: 'dayGridMonth',  // 确保是按天视图显示
          weekends: true,
          // events: [],
          events: [
            { title: '日内下受阻数据未上报', start: '2025-02-01' },
            { title: '日内下受阻数据未上报', start: '2025-02-01' },
            { title: '日内下受阻数据未上报', start: '2025-02-01' },
            { title: '日内下受阻数据未上报', start: '2025-02-01' },
            { title: '日内下受阻数据未上报', start: '2025-02-01' },
            { title: '日内下受阻数据未上报', start: '2025-02-02' },
            { title: '日内下受阻数据未上报', start: '2025-02-02' },
            { title: '日内下受阻数据未上报', start: '2025-02-03' },
            { title: '日内下受阻数据未上报', start: '2025-02-03' },
            { title: '日内下受阻数据未上报', start: '2025-02-03' },
          ],
        }
      };
    },
    mounted() {
      this.fetchTableData();
    },
    methods: {
      async fetchTableData() {
        try {
          const response = await axios.get(scanPath, { 
            params: { 
              startDate: this.startDate, 
              areaId: this.areaId, 
            }
          });
          if (response.data.code === 200) {
            this.rawData = response.data.data;
            this.loading = false;
            // this.processRawData();
            // this.calendarOptions.events = this.calendarData;
          } else {
            this.rawData = [];
          }
        } catch (error) {
          console.error('Error fetching file list:', error);
        }
        console.log(this.calendarOptions.event)
        this.loading = false;
      },
      processRawData() {
        this.calendarData = this.rawData.map(item => {
          return {
            title: item.name || '',
            start: item.createDate || '',
          }
        }).filter(item => item !== null);
      },
    }
  };
</script>
  
<style lang="scss">  

::v-deep .fc-day 
::v-deep .fc-day-sat
::v-deep .fc-day-past
::v-deep .fc-daygrid-day{
  background-color: hsl(0, 81%, 43%) !important;
}


</style>  