<template>
  <v-timeline-item
    dot-color="#e52c4d"
    :size="timelineSize"
  >
    <template #opposite>
      <span class="timeline-item__year">{{ item.year }}</span>
    </template>

    <v-card
      class="timeline-item__card"
      color="#f4f4f4"
      :data-aos="animation"
    >
      <div class="timeline-item__thumbnail-container">
        <a
          :href="item.link"
          target="_blank"
          @click="sendTimelineCardClickAnalyticsEvent"
        >
          <GenericImage
            class="image-animation"
            :src="path"
            :lazy-src="path"
            :alt="item.cover"
            cover
          />
        </a>
      </div>

      <v-card-title class="timeline-item__title">
        {{ $t(`${prefix}.title`) }}
      </v-card-title>

      <v-card-subtitle
        class="timeline-item__year"
        v-if="smAndDown"
      >
        {{ $t(`${prefix}.year`) }}
      </v-card-subtitle>

      <v-card-subtitle class="timeline-item__location">
        {{ $t(`${prefix}.location`) }}
      </v-card-subtitle>

      <v-card-subtitle
        class="timeline-item__description"
        v-if="smAndUp"
      >
        <div v-html="$t(`${prefix}.description`)" />
      </v-card-subtitle>
    </v-card>
  </v-timeline-item>
</template>

<script setup lang="ts">
import type { Experience } from '@/types/Experience'

import GenericImage from '@/components/GenericImage/GenericImage.vue'
import { useImagePath } from '@/composables/common/image-path'
import { usePrefixTranslation } from '@/composables/common/prefix-translation'
import { useGoogleAnalyticsEvent } from '@/composables/event/google-analytics'
import { useAnimation } from '@/composables/style/animation'
import { useResponsive } from '@/composables/style/responsive'
import { toRef } from 'vue'
import { useDisplay } from 'vuetify'

const { componentName, index, item } = defineProps<{
  componentName: string
  index: number
  item: Experience
}>()

const { timelineSize } = useResponsive()
const { smAndDown, smAndUp } = useDisplay()
const prefix = usePrefixTranslation(toRef(() => componentName), toRef(() => index))

const { path } = useImagePath({
  directory: componentName,
  image: item.cover,
})

function sendTimelineCardClickAnalyticsEvent(): void {
  useGoogleAnalyticsEvent({
    action: `timeline-card:click`,
    category: componentName,
    label: item.title,
  })
}

const { animation } = useAnimation({
  componentType: 'timeline',
  index,
})
</script>

<style lang="scss" src="./timeline-road-item.scss" scoped />
