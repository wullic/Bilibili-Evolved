<template>
  <div class="bangumi-feeds">
    <VLoading v-if="loading"></VLoading>
    <VEmpty v-else-if="!loading && cards.length === 0"></VEmpty>
    <template v-else>
      <div class="bangumi-feeds-content">
        <BangumiCard v-for="c of cards" :key="c.id" :is-new="c.new" :data="c"></BangumiCard>
      </div>
      <ScrollTrigger v-if="hasMorePage" @trigger="nextPage()"></ScrollTrigger>
    </template>
  </div>
</template>
<script lang="ts">
import { feedsCardTypes } from '@/components/feeds/api'
import { isNewID } from '@/components/feeds/notify'
import { BangumiCard } from '@/components/feeds/bangumi-card'
import BangumiCardComponent from '@/components/feeds/BangumiCard.vue'
import { nextPageMixin } from './next-page'

export default Vue.extend({
  components: {
    BangumiCard: BangumiCardComponent,
  },
  mixins: [
    nextPageMixin(feedsCardTypes.bangumi, (card: any) => {
      const cardJson = JSON.parse(card.card)
      return {
        id: card.desc.dynamic_id_str,
        title: cardJson.apiSeasonInfo.title,
        coverUrl: cardJson.apiSeasonInfo.cover,
        epCoverUrl: cardJson.cover,
        epTitle: cardJson.new_desc,
        url: cardJson.url,
        get new() {
          return isNewID(this.id)
        },
      } as BangumiCard
    }),
  ],
})
</script>
<style lang="scss">
.bangumi-feeds {
  display: flex;
  flex-direction: column;
  align-items: center;
  .bangumi-feeds-content {
    align-self: stretch;
  }
  .be-scroll-trigger {
    padding-bottom: 12px;
  }
}
</style>
