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
            cycle=true,
            interval=5000
          >
            <v-carousel-item v-for="(item,i) in filteredItems"
                            :key="i">
              <v-card>
                <v-card-title>{{ item.caption }}</v-card-title><br>

                <v-img :src=item.src :cover=false width="99%"></v-img><br>
                <v-card-item max-height="70%" >
                  <v-card-text vert>{{ item.text }}</v-card-text>
                </v-card-item>

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
    ],
  }),
  computed: {
    filteredItems() {
      return this.items.filter((e,self) => {
        return !this.tag || e.category == this.tag
      });
    }
  },
  watch: {
    tag(value) {
      this.carousel = 0;
    }
  }
};
</script>


<style scoped>


</style>
