<template>
  <EternaPage v-if="pageData">
    <b-container
      class="video"
      :style="{
        backgroundImage: `linear-gradient(to bottom, rgba(0, 0, 0, 0), rgba(0, 0, 0, 0.75)),url('${
          pageData[`banner-image`]
        }')`,
      }"
    >
      <div class="lending_content">
        <div>
          <p style="font-size: 2.8rem; font-weight: bold;">{{ pageData[`banner-title`] }}</p>
          <p>
            {{ pageData[`banner-sub-title`].toUpperCase() }}
          </p>
          <b-button variant="primary" size="lg" to="/game/puzzle/6502927/">Enter Lab</b-button>
        </div>
        <div class="d-flex">
          <div class="charts">
            <progress class="test" value="44" max="100" :style="'--progress: 50'"></progress>
          </div>
          <div class="charts">
            <progress class="test" value="44" max="100" :style="'--progress: 50'"></progress>
          </div>
        </div>
      </div>
    </b-container>

    <h1 :style="{ fontSize: '36px', fontWeight: 'bold', marginTop: '50px', marginBottom: '15px' }">
      {{ $t('player-home:puzzles') }}
    </h1>
    <Swiper class="swiper" :options="swiperOption">
      <swiper-slide class="swiper_box" v-for="(item, index) in puzzles" :key="index">
        <QuestCard :nid="index" title="test" leftNumber="1" rightNumber="2" states="0"/>
      </swiper-slide>
      <div class="swiper-pagination" slot="pagination"></div>
      <div class="swiper-button-prev" slot="button-prev"></div>
      <div class="swiper-button-next" slot="button-next"></div>
    </Swiper>

    <h1 :style="{ fontSize: '36px', fontWeight: 'bold', marginTop: '50px', marginBottom: '15px' }">
      {{ $t('player-home:quests') }}
    </h1>
    <Swiper class="swiper_2" :options="swiperOption">
      <swiper-slide class="swiper_box" v-for="(item, index) in quests" :key="index">
        <QuestCard :nid="index" :progress="item"/>
      </swiper-slide>
      <div class="swiper-pagination oagination_2" slot="pagination"></div>
      <div class="swiper-button-prev" slot="button-prev"></div>
      <div class="swiper-button-next" slot="button-next"></div>
    </Swiper>
  </EternaPage>
</template>

<script lang="ts">
  import { Component, Vue, Mixins } from 'vue-property-decorator';
  import { RouteCallback, Route } from 'vue-router';
  import { AxiosInstance } from 'axios';
  import { Swiper, SwiperSlide } from 'vue-awesome-swiper';
  import EternaPage from '@/components/PageLayout/EternaPage.vue';
  import PageDataMixin from '@/mixins/PageData';
  import PuzzleCard from '@/components/Cards/PuzzleCard.vue';
  import QuestCard from '@/components/Cards/QuestCard.vue';
  import 'swiper/css/swiper.css';

  async function fetchPageData(route: Route, http: AxiosInstance) {
    return (await http.get(`/get/?type=user&uid=${route.params.uid}`)).data.data;
  }

  @Component({
    components: {
      EternaPage,
      PuzzleCard,
      QuestCard,
      Swiper,
      SwiperSlide,
    },
  })
  export default class ExperiencedPlayerView extends Mixins(PageDataMixin(fetchPageData)) {
    get pageData() {
      return {
        'banner-title': 'Optimizing the Ribosome',
        'banner-sub-title': 'Ribosome Design Challenge',
        'banner-image':
          'https://cdn.zeplin.io/5e88563a3843011f95808b2f/assets/11FA9E9F-89F8-4548-A93F-241E4D1D6362.png',
      };
    }

    private picture: string = `${process.env.VUE_APP_API_BASE_URL}/sites/default/files/pictures/picture-133043.png`;

    // private picture: string = 'https://graph.facebook.com/10220887579400634/picture?type=normal';

    private playerName: string = 'Iroppy';

    private playerRank: string = '1';

    private windowWidth = window.innerWidth;

    mounted() {
      window.onresize = () => {
        this.windowWidth = window.innerWidth;
      };
    }

    private swiperOption = {
      slidesPerView: this.windowWidth < 400 ? 3 : 4,
      spaceBetween: 30,
      loop: false,
      pagination: {
        el: '.swiper-pagination',
        clickable: true,
      },

      navigation: {
        nextEl: '.swiper-button-next',
        prevEl: '.swiper-button-prev',
      },
    };

    private puzzles: number[] = [1, 2, 3, 4, 5, 6];

    private quests: number[] = [40, 20, 30, 10, 50, 70];
  }
</script>

<style lang="scss" scoped>
  @import '@/styles/global.scss';

  @supports(-webkit-appearance: none) {

    progress, meter {
    -webkit-appearance: none;
      appearance: none;
    }

    /*gets rid of all pseudo elements*/
    ::-webkit-progress-inner-element, ::-webkit-progress-bar, ::-webkit-progress-value {
      display: none;
    }

    ::-webkit-meter-bar, ::-webkit-meter-optimum-value, ::-webkit-meter-suboptimum-value, ::-webkit-meter-even-less-good-value {
      display: none;
    }

    /* overlays text*/
    .test:after {
      content: attr(value) "";
      position: absolute;
      top: 6px;
      right: 6px;
      bottom: 6px;
      left: 6px;
      background-Image: linear-gradient(to bottom, rgba(4, 0, 5, 0), rgba(6, 3, 4, 0.75)),url( 'https://cdn.zeplin.io/5e88563a3843011f95808b2f/assets/11FA9E9F-89F8-4548-A93F-241E4D1D6362.png');
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: bold;
      font-size: 30px;
      line-height: 45px;
      color: #524F63;
    }

    /*using a conical gradient to create the doughnut chart  */
    .test {
      position: relative;
      width: 150px;
      height: 150px;
      border-radius: 50%;
      --fill: calc(var(--progress) * 1%);
      background: conic-gradient(#48C89E var(--fill), red 0);
      @media (max-width: 500px) {
        width: 110px;
        height: 110px;
      }
    }

    /* just to change the fill color*/
    meter.test {
      background: conic-gradient(#FF7676 var(--fill), red 0);
    }

  }


  .video {
    background-position: right;
    background-repeat: no-repeat;
    object-fit: contain;
    @include media-breakpoint-up(sm) {
      height: 519px;
      padding: 31px;
      padding-top: 322px;
    }
    height: 400px;
    padding-top: 91px;
    width: 100%;
  }

  .video-wrapper {
    background-color: $dark;
    padding-top: 0.625rem;
    border-radius: 0.3125rem;
  }

    .swiper_box {
      width: 17.1875rem;
      height: 17.1875rem;
      border-radius: 0.3125rem;
      background-color: $input-bg;
      padding-top: 2.1875rem;
      @media (max-width: 1000px) {
        height: 14.3125rem;
        padding-top: 0.1875rem;;
      }
    }

  .swiper-pagination {
    bottom: unset;
  }

  .oagination_2 {
    bottom: -11px;
    display: block;
  }

  .swiper-button-next:after, .swiper-button-prev:after {
    font-size: 1.25rem;
  }

  .swiper-button-next, .swiper-button-prev {
    width: 47px;
    height: 34px;
    background-color: $black;
    top: 57%;
  }

  .swiper_2 {
    position: unset;

    .swiper-button-next, .swiper-button-prev {
      top: 87%;
      position: absolute;
    }
  }


  .swiper-button-next {
    right: -10px;
    border-radius: 13px 0 0 13px;
  }

  .swiper-button-prev {
    left: -10px;
    border-radius: 0 13px 13px 0;
  }

  .swiper-button-prev.swiper-button-disabled, .swiper-button-next.swiper-button-disabled {
    display: none;
  }


  .swiper {
    position: unset;
  }

  .lending_content {
    display: flex;
    justify-content: space-between;
  }
</style>
