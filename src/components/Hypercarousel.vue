<template >
  <v-container>
    <v-row >
      <v-col cols="2">
        <v-chip-group :multiple="false"  direction="vertical" v-model="tag">
<!--          TODO: compute the categories from the items data.-->
          <v-chip value='animal' color="red">animals</v-chip>
          <v-chip value='cosmology' color="green">cosmology</v-chip>
          <v-chip value = 'plant' color="orange">plants</v-chip>
        </v-chip-group>
        <h1>{{tag}}</h1>
      </v-col>
      <v-col cols="10">
        <v-carousel :hide-delimiters="true"
            v-model="carousel"
            reverse-transition="fade-transition"
            transition="fade-transition"
            interval=9000
            xcycle
            height=1000
          >
            <v-carousel-item v-for="(item,i) in filteredItems"
                            :key="i">
              <v-card >
                <v-card-item padding="0" margin="0">
                  <v-card-title>{{ item.caption }}</v-card-title>
                  <v-card-text>{{ item.text }}</v-card-text>

                </v-card-item>
                <v-img v-if="item.src.search(/.jpg|jpeg|png$/) > 0" :src=item.src :cover=false width="99%"></v-img>
                <template v-if="item.src.endsWith('.mp4')" >
<!--                  TODO: make this not cycle if the video is playing?-->
                  <video :id="`video${i}`" controls height="540"  preload="auto" >
                    <source :src="item.src" type="video/mp4" />
                  </video>
                </template>
              </v-card>
            </v-carousel-item>
        </v-carousel>
<!--        cycle? {{ interval }} {{ !!cycle }}-->

      </v-col>
    </v-row>
  </v-container>
</template>

<script  lang="ts">
import axios from 'axios';

export default {
  name: "Hypercarousel",
  props: [
    'url'
  ],
  data: () => ({
    tag: undefined,
    error: undefined,
    isLoading: false,
    carousel: 0,
    items: []
  }),
  computed: {
    filteredItems() {
      return this.items.filter((e,self) => {
        console.debug(self);
        return !this.tag || e.category == this.tag;
      });
    },
    vidVisible(id: string) {
      const el = document.getElementById(id);
      if(!el) {
        return false;
      }
      console.log(el);
      return true;
    }
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    videoOff(item: any) {   // just turn off any video that's playing.
      const id = 'video' + item;
      const videos: HTMLCollection = document.getElementsByTagName('video');
      console.log(videos);
      if(videos.length > 0) {
        const vidArray = Array.from(videos);
        vidArray.map( (video: any) => {
          console.log(video);
          console.log('playing! ', video.id, '  - pausing');
          video.pause();    // will this really work?

        })
      }
    },
    async fetchData() {
      this.isLoading = true;  // Set loading to true
      try {
        const response = await axios.get(this.url);
        this.items = response.data;  // Store the fetched data
      } catch (error: any) {
        this.error = error.message;   // Set error message if fetching fails
      } finally {
        this.isLoading = false;  // Set loading to false
      }
    },
  },
  watch: {
    tag() {
      this.carousel = 0;
    },
    carousel() {
      this.videoOff(this.carousel);
    }
  }
};
</script>


<style scoped>
 video {
   object-fit: fill;
   padding: 5px;
   margin: 5px;
 }

</style>
