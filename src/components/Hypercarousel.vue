<template >
  <v-container>
    {{error}}
    <v-row >
      <v-col cols="3">
        <v-chip-group :multiple="false"  direction="vertical" v-model="tag">
          <v-chip v-for="(t,i) in tags" :value=t
                  :key="i">{{t}}</v-chip>
        </v-chip-group>
        <h6>{{tag}}</h6>
      </v-col>
      <v-col cols="9">
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
                  <video :id="`video${i}`" controls height="540"  preload="auto" >
                    <source :src="item.src" type="video/mp4" />
                  </video>
                </template>
              </v-card>
            </v-carousel-item>
        </v-carousel>
      </v-col>
    </v-row>
  </v-container>
</template>

<script  lang="ts">
import axios from 'axios';

// TODO: i know i'm not doing this type stuff right.
type CarouselItem = {
  caption: string;
  text: string;
  src: string;
  category: string;
}

type FetchResponseType = {
  data: CarouselItem[];     // an array of items.
}

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
    tags() { return [... new Set(this.items.map((i: any) => i.category))]},
    filteredItems() {
      return this.items.filter((e: any, self) => {    // self is needed just so "this" is preserved.
        return !this.tag || e.category == this.tag;
      });
    },

  },
  mounted() {
    this.fetchData();
  },
  methods: {
    videoOff() {   // just turn off any video that's playing.
      // const id = 'video' + item;
      const videos: HTMLCollection = document.getElementsByTagName('video');
      // console.log(videos);
      if(videos.length > 0) {
        const vidArray = Array.from(videos);
        vidArray.map( (video: any) => {
          console.debug(video);
          console.debug('playing! ', video.id, '  - pausing');
          video.pause();    // will this really work?

        })
      }
    },
    async fetchData() {
      this.isLoading = true;  // Set loading to true
      // url has to be relative if dev mode, absolute if production
      let fetchUrl: string = this.url;
      if(process.env.NODE_ENV === 'development') {
        fetchUrl = 'xportfolio.json'
      }
      console.debug('fetching from', fetchUrl);
      try {
        const { data } = await axios.get(fetchUrl);   // TODO: look at if the data is an array
        if(!(data && Array.isArray(data) && typeof data[0] === 'object' )) {
          throw Error(`Error: data fetched from ${fetchUrl} is wrong shape`)
        }
        this.items = data;  // Store the fetched data
      } catch (error: any) {
        console.log(error);
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
      this.videoOff();
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
