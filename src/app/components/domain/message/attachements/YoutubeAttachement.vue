<script setup lang="ts">
import type { YoutubeMessageAttachement } from "@/modules/message/models/domain";

const props = defineProps<{
  attachement: YoutubeMessageAttachement;
}>();

</script>
<template>
  <div class="youtube-attachement light-card">
    <div class="youtube-attachement-domain"><!-- TODO --></div>
    <iframe :src="'https://www.youtube.com/embed/' + attachement.videoId"></iframe>
    <iframe width="560" height="315" src="https://www.youtube.com/embed/jHEpgsjknCs?si=xdOMPXDMvjfN0Lm4&amp;start=1"
      title="YouTube video player" frameborder="0"
      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
      allowfullscreen></iframe>
  </div>
</template>
<style lang="scss" scoped>
@use "@/app/styles/var";
@use "sass:map";

.youtube-attachement {
  max-width: 350px;

  .youtube-attachement-domain {
    font-size: 14px;
    color: var.$color-dark-gray;
  }

  >iframe {
    margin-top: map-get(var.$spaces, "2xs");
    aspect-ratio: 16 / 9;
    width: 100%;
    display: block;
  }
}</style>
