<template>
  <SidebarPanel :isInSidebar="isInSidebar" header="CALENDAR" headerIcon="@/assets/sidebar/calendar.svg">
      <calendar v-on:chooseDay="chooseDay" ></calendar>
  </SidebarPanel>

</template>

<script lang="ts">
  import { Component, Prop, Vue } from 'vue-property-decorator';
  import { mixins } from 'vue-class-component';
  import SidebarPanel from '@/components/Sidebar/SidebarPanel.vue';
  import SidebarPanelMixin from '@/mixins/SidebarPanel';
  import Calendar from '@/components/Global/Calendar.vue';

  @Component({
    components: {
      SidebarPanel,
      Calendar,
    },
  })
  export default class CalendarPanel extends mixins(SidebarPanelMixin) {
    chooseDay(val: string) {
      // alert(val)
    }

    @Prop() tags!: string[];
  }
</script>

<style scoped lang="scss"></style>
