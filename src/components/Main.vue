<script>
import Title from './sections/Title.vue'
import Abstract from './sections/Abstract.vue'
import MarkdownMeasurement from './sections/MarkdownMeasurement.vue'
import MarkdownDesign from './sections/MarkdownDesign.vue'
import Carousel from './sections/Carousel.vue'
import Video from './sections/Video.vue'

export default {
  components: {
    Title,
    Abstract,
    MarkdownMeasurement,
    MarkdownDesign,
    Carousel,
    Video,
  }
}
</script>

<template>
  <Title/>
  <Abstract/>
  <MarkdownMeasurement/>
  <MarkdownDesign/>
  <Carousel/>
  <Video/>
</template>