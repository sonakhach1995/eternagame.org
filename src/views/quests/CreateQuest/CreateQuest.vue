<template>
  <EternaPage title="Create Quest">
    <template #sidebar="{ isInSidebar }">
<!--      <LabInfoPanel :lab="lab" :isInSidebar="isInSidebar" />-->
      <SidebarPanel header="TIPS & RESOURCES" isInSidebar="true">
        <div>
          <b>Lectus a mollis volutpat elementum</b>
          <p>Malesuada ullamcorper a interdum a volutpat dui vestibulum semper ornare inceptos mus iaculis eleifend.</p>
        </div>
        <div>
          <b>Eu sodales turpis </b>
          <p>Dignissim hendrerit eleifend id nam nunc augue duis id a interdum mattis lectus nec proin.</p>
        </div>
        <div>
          <b>Adipiscing metus </b>
          <p>auctor cum nisl consectetur mi ridiculus iaculis id phasellus fermentum posuere a curabitur faucibus porttitor aptent fringilla arcu a bibendum in ut parturient sit.</p>
        </div>


      </SidebarPanel>
    </template>
  </EternaPage>
</template>

<script lang="ts">
  import { Component, Vue, Mixins } from 'vue-property-decorator';
  import { RouteCallback, Route } from 'vue-router';
  import { AxiosInstance } from 'axios';
  import SidebarPanel from '@/components/Sidebar/SidebarPanel.vue';
  import EternaPage from '@/components/PageLayout/EternaPage.vue';
  import PageDataMixin from '@/mixins/PageData';
  import TagsPanel from '@/components/Sidebar/TagsPanel.vue';
  import LabViewData, { LabData } from './types';

  async function fetchPageData(route: Route, http: AxiosInstance) {
    return (await http.get(`/get/?type=project&nid=${route.params.id}`)).data.data as LabViewData;
  }

  @Component({
    components: {
      SidebarPanel,
      EternaPage,
      TagsPanel,
    },
  })
  export default class CreateQuest extends Mixins(PageDataMixin(fetchPageData)) {
    get lab() {
      return this.pageData?.lab;
    }
  }
</script>
