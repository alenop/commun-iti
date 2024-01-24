<script setup lang="ts">
import { useProvider } from "@/app/platform";
import { onMounted, ref, type Ref } from "vue";
import { HtmlOgParser, type OgMetadata } from "@/modules/infrastructure/HtmlOgParser";
import type { WebsiteMessageAttachement } from "@/modules/message/models/domain";

interface WebsiteMetadata {
  domain: string;
  title?: string;
  description?: string;
  imageUrl?: string;
}

const [ogParser] = useProvider([HtmlOgParser]);

const props = defineProps<{
  attachement: WebsiteMessageAttachement;
}>();
let data: Ref<WebsiteMetadata> = ref({
  domain: "",
  title: "",
  description: "",
  imageUrl: ""
});

onMounted(async () => {
  const d = await ogParser.parse(props.attachement.url);
  if (d) {
    data.value = d;
  }
})
</script>
<template>
  <div v-if="data" class="website-attachement light-card">
    <div class="website-attachement-domain">
      {{ data.domain }}
    </div>

    <div class="website-attachement-title">
      {{ data.title }}
    </div>

    <div class="website-attachement-description">
      {{ data.description }}
    </div>

    <div class="website-attachement-image">
      <img :src="data.imageUrl">
    </div>
  </div>
</template>
<style lang="scss" scoped>
@use "sass:map";
@use "@/app/styles/var";

.website-attachement {
  max-width: 450px;

  .website-attachement-domain {
    font-size: 14px;
    color: var.$color-dark-gray;
  }

  .website-attachement-image {
    max-width: 350px;
    margin-top: map-get(var.$spaces, "2xs");

    img {
      width: 100%;
      display: block;
    }
  }
}
</style>
