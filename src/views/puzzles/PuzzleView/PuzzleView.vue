<template>
  <EternaPage v-if="puzzle" :title="puzzle.title">
    <div class="page-content">
      <div class="d-flex flex-column">
        <div>
          <h2>{{ $t('puzzle-view:main-header') }}</h2>
          <p>{{ puzzle.about }}</p>
        </div>
        <div class="second_content d-flex">
        <div>
          <h3>{{ $t('puzzle-view:auxiliary-header-one') }}</h3>
          <p>{{ puzzle.science }}</p>
          <h3>{{ $t('puzzle-view:auxiliary-header-two') }}</h3>
          <p>{{ puzzle.mission }}</p>
          <h3>{{ $t('puzzle-view:auxiliary-header-three') }}</h3>
          <p>{{ puzzle.information }}</p>
        </div>

        <div style="text-align:center">
          <div class="content_img"></div>
          <b-button type="submit" variant="primary" class="submit-button">
            {{ $t('puzzle-view:main-action') }}
          </b-button>
        </div>
      </div>
      </div>
      <hr class="top-border" />

      <h2>{{ $t('nav-bar:quests') }}</h2>
      <p>{{ $t('puzzle-view:quests-info') }}</p>
      <div class="images">
        <img
          v-for="questImage in puzzle.quests"
          :key="questImage"
          :src="questImage"
          alt="quest"
          class="quest-image"
        />
      </div>
    </div>

    <template #sidebar="{ isInSidebar }">
      <SidebarPanel
        :isInSidebar="isInSidebar"
        :header="$t('puzzle-view:side-bar-header')"
        headerIcon="@/assets/info.svg"
      >
        <template #header-icon>
          <img src="@/assets/info.svg" />
        </template>
        <ul style="padding: 0; list-style-type:none">
          <li v-if="puzzle.reward">
            <img src="@/assets/dollar.svg" class="icon" />{{ puzzle.reward }}
          </li>
          <li v-if="puzzle.audience">
            <img src="@/assets/people.svg" class="icon" />{{ puzzle.audience }}
          </li>
          <li v-if="puzzle.created">
            <img src="@/assets/calendar.svg" class="icon" />{{ puzzle.created }}
          </li>
        </ul>
      </SidebarPanel>
      <TagsPanel :tags="['#SRP', '#easy']" :isInSidebar="isInSidebar" />
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
  import PuzzleData from './types';

  async function fetchPageData(route: Route, http: AxiosInstance) {
    const res = (
      await http.get(`/get/?type=puzzle&nid=${route.params.id}&script=-1`, {
        params: {
          order: route.query.sort,
          filters: route.query.filters && (route.query.filters as string).split(','),
        },
      })
    ).data.data as PuzzleData;
    return res;
  }

  @Component({
    components: {
      EternaPage,
      TagsPanel,
      SidebarPanel,
    },
  })
  export default class PuzzleView extends Mixins(PageDataMixin(fetchPageData)) {
    get puzzle() {
      // return this.pageData;
      return {
        title: 'SRP RNA - Difficulty Level 0',
        reward: 140,
        created: 'Sept 2010',
        audience: 16411,
        about: 'Welcome to the easiest puzzle of the SRP RNA series!',
        science:
          'The SRP RNA, also known as 7SL, 6S, or 4.5S RNA, is the RNA component of the signal recognition particle (SRP) ribonucleoprotein complex. SRP is a universally conserved ribonucleoprotein that directs the traffic of proteins within the cell and allows them to be secreted. ',
        mission: 'Design the SRP RNA with following specifications: • Minimum 5 GU Pairs',
        information:
          'For more information please visit: Crystal structure Image and Protein Data Bank',
        quests: [
          'https://cdn.zeplin.io/5e88563a3843011f95808b2f/assets/5ED5D090-6F62-4DF8-8C54-CC71306A4B16.png',
          'https://cdn.zeplin.io/5e88563a3843011f95808b2f/assets/6A70A1E1-9A81-4BA0-B765-A12B8F821300.png',
          'https://cdn.zeplin.io/5e88563a3843011f95808b2f/assets/E280848F-6347-4CC5-A215-F08B1F55ED1B.png',
        ],
      };
    }
  }
</script>

<style scoped lang="scss">

  .images {
    @media (max-width: 500px) {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
    }
    .quest-image {
      margin-right: 2.1875rem;
      @media (max-width: 570px) {
        margin: 0 0.625rem 0 0;
      }
    }
  }

  .submit-button {
    margin: 1.0625rem 0 1.8125rem 0;
    width: 9.8125rem;
  }
  h2 {
    font-size: 1.375rem;
    font-weight: bold;
  }
  h3 {
    font-size: 12px;
    text-transform: uppercase;
    font-weight: bold;
  }
  p {
    font-size: 1.0625rem;
    line-height: 1.47;
    @media (max-width: 650px) {
      font-size: 0.75rem;
    }
    @media (max-width: 450px) {
      font-size: 0.5rem;
    }
    @media (max-width: 375px) {
      font-size: 0.875rem;
    }
  }

  .icon {
    margin-right: 10px;
    width: 20.4px;
    height: 20px;
    object-fit: contain;
  }

  li {
    margin-top: 1.375rem;
    font-size: 1.0625rem;
  }
</style>
