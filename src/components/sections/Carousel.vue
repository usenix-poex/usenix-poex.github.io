<script>
import { Swiper, SwiperSlide} from 'swiper/vue';
import { Navigation, Pagination, Autoplay} from 'swiper/modules';
import 'swiper/css/bundle';

export default {
  components: {
      Swiper,
      SwiperSlide,
      Navigation,
      Pagination,
      Autoplay,
    },
  data() {
    return {
      modules: [
        Navigation,
        Pagination,
        Autoplay,
      ],
      image_paths: [
        "./carousel/1.png",
        "./carousel/2.png",
        "./carousel/3.png",
        "./carousel/4.png",
      ],
    }
  }
}
</script>

<template>
  <div>
    <el-divider />

    <el-row justify="center">
      <h1 class="section-title">Real-World Attack Pipeline</h1>
    </el-row>

    <br />

    <el-row justify="center">
      <el-col :span="24">
      <swiper
        :loop="true"
        :slidesPerView="1"
        :breakpoints="{
          600: {
            slidesPerView: 2,
          },
          800: {
            slidesPerView: 3,
          },
        }"
        :modules="modules"
        :navigation="{ 
          hideOnClick:true,
        }"
        :pagination="{ 
          hideOnClick:true,
          clickable:true, 
          type:'bullets' 
        }"
        :autoplay="{ 
          delay:5000,
          disableOnInteraction:false,
          pauseOnMouseEnter:true,
        }"
        >
        <swiper-slide v-for="path in image_paths">
          <el-image :src="path"/>
        </swiper-slide>
      </swiper>
    </el-col>
  </el-row>
  </div>
</template>
  
<style>

.swiper {
  --swiper-theme-color: white;
}

</style>
  