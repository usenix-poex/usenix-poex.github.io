<script>
import Title from './sections/Title.vue'
import Abstract from './sections/Abstract.vue'
import MarkdownMeasurement from './sections/MarkdownMeasurement.vue'
import MarkdownDesign from './sections/MarkdownDesign.vue'
import Carousel from './sections/Carousel.vue'
import Video from './sections/Video.vue'
import HarmfulInstances from './sections/HarmfulInstances.vue'

export default {
  components: {
    Title,
    Abstract,
    MarkdownMeasurement,
    MarkdownDesign,
    Carousel,
    Video,
    HarmfulInstances,
  }
}
</script>

<template>
  <Title/>
  <Abstract/>
  <MarkdownMeasurement/>
  <HarmfulInstances/>
  <MarkdownDesign/>
  <Carousel/>
  <Video/>
</template>