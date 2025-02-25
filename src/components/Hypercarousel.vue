<template >
  <v-container>
    <v-row >
      <v-col cols="2">
        <v-chip-group :multiple="false"  direction="vertical" v-model="tag">
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
            cycle
            height="1000"
          >
            <v-carousel-item v-for="(item,i) in filteredItems"
                            :key="i">
              <v-card>
                <v-card-item padding="0" margin="0">
                  <v-card-title>{{ item.caption }}</v-card-title>
                  <v-card-text>{{ item.text }}</v-card-text>

                </v-card-item>
                <v-img v-if="item.src.search(/.jpg|jpeg|png$/) > 0" :src=item.src :cover=false width="99%"></v-img>
                <template v-if="item.src.endsWith('.mp4')">
<!--                  TODO: make this not cycle if the video is playing?-->
                  <video controls  height="540">
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

export default {
  name: "Hypercarousel",
  data: () => ({
    tag: undefined,
    carousel: 0,
    items: [
      {
        src: "https://cdn.vuetifyjs.com/images/carousel/squirrel.jpg",
        caption: "Red Squirrel",
        text: "A really cool little varmint that buries nuts.",
        category: "animal"
      },
      {
        src: "https://cdn.vuetifyjs.com/images/carousel/sky.jpg",
        caption: "Sky",
        text: "The ceiling of our world.",
        category: "cosmology"
      },
      {
        src: "https://cdn.vuetifyjs.com/images/carousel/bird.jpg",
        caption: "Bird",
        text: "Rats of the sky. Vermin who foul our atmosphere.",
        category: "animal"
      },
      {
        src: "https://www.detritus.net/cine/TFW_NO_GF_2020.mp4",
        caption: "TFWNOGF",
        text: "this is a good documentary",
        category: "cosmology"
      }
    ],
  }),
  computed: {
    filteredItems() {
      return this.items.filter((e,self) => {
        console.log(self);
        return !this.tag || e.category == this.tag;
      });
    }
  },
  // methods: {
  //   // returns an object in the shape that VuetifyPlayer wants.
  //   videoSource(src: string) {
  //     return {
  //       sources: [
  //         {
  //           src,
  //           type: 'video.mp4',
  //         }
  //       ],
  //     }
  //   }
  // },
  watch: {
    tag(value) {
      this.carousel = 0;
    }
  }
};
</script>


<style scoped>
  body {
    background-color: #999999;
  }

</style>
