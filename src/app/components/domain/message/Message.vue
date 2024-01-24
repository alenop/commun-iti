<script lang="ts" setup>
import RichText from "@/app/components/ui/RichText.vue";
import BgImage from "../../ui/BgImage.vue";
import ItiEmojiPicker from "../../ui/emoji-picker/EmojiPicker.vue";
import EmojiIcon from "../../ui/icons/EmojiIcon.vue";
import MessageAttachements from "./MessageAttachements.vue";
import { useProvider } from "@/app/platform";
import { MessageService } from "@/modules/message/services/MessageService";
import { AuthenticationStore } from "@/modules/authentication/store/AuthenticationStore";
import { DateTime } from "luxon";
import { useState } from "@/app/platform";
import { MessageStore } from "@/modules/message/store";
import { RoomStore } from "@/modules/room/store";
import { MessageAPI } from "@/modules/message/services/MessageAPI";
import { RoomAPI } from "@/modules/room/services";

import MessageReactions, { type MessageReaction } from "./MessageReactions.vue";
import ImageAttachement from "./attachements/ImageAttachement.vue";
import VideoAttachement from "./attachements/VideoAttachement.vue";
import WebsiteAttachement from "./attachements/WebsiteAttachement.vue";
import YoutubeAttachement from "./attachements/YoutubeAttachement.vue";
import { type ImageMessageAttachement, type Message, type VideoMessageAttachement, type WebsiteMessageAttachement, type YoutubeMessageAttachement } from "@/modules/message/models/domain";
const authState = useState(AuthenticationStore);
const props = defineProps<{
  message: Message;
}>();
function getDate() {
  return new Date().toLocaleDateString('fr-FR', {
    year: 'numeric',
    month: 'long',
    day: '2-digit',
  });
}
async function onEmojiPicked(emoji: string) {
  if (authState.loggedUser && props.message.reactions.find(obj => obj.emoji === emoji)?.userReactions.some((reaction) => reaction.userId === authState.loggedUser?.id)) {
    await messageSerivce.removeReaction(emoji, props.message);
  } else {
    console.log(authState.loggedUser?.id);
    await messageSerivce.reactTo(emoji, props.message);
  }

}

const [messageSerivce] = useProvider([MessageService]);

</script>

<template>
  <div class="message">
    <div class="message-actions">
      <iti-emoji-picker ref="emojiPicker" @pick="onEmojiPicked" />
      <el-button :icon="EmojiIcon" circle size="small" @click="$refs.emojiPicker.show()" />
    </div>

    <bg-image class="message-user-photo" :src="message.author.pictureUrl" />

    <div class="message-content">
      <div v-for="attachment, index in message.attachements" :key="index">
        <youtube-attachement v-if="attachment.type == 'youtube'"
          :attachement="(attachment as YoutubeMessageAttachement)"></youtube-attachement>
        <website-attachement v-else-if="attachment.type == 'website'"
          :attachement="(attachment as WebsiteMessageAttachement)"></website-attachement>
        <video-attachement v-else-if="attachment.type == 'video'"
          :src="(attachment as VideoMessageAttachement).src"></video-attachement>
        <image-attachement v-else-if="attachment.type == 'image'"
          :src="(attachment as ImageMessageAttachement).src"></image-attachement>
      </div>

      <div class=" message-title">{{ message.author.username }}
        <small class="message-date">{{ getDate() }}</small>
        <rich-text :text="message.text"></rich-text>
        <div class="tropgood">
          <div v-for="reaction, index in message.reactions" :key="index"
            :class="{ 'user-reacted': authState.loggedUser && reaction.userReactions.some((reaction) => reaction.userId === authState.loggedUser?.id) }">
            <button @click="onEmojiPicked(reaction.emoji)">{{ reaction.emoji }}{{
              reaction.userReactions.length }}</button>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<style lang="scss">
@use "sass:map";
@use "@/app/styles/var";

.message {
  margin: 0;
  padding: map-get(var.$spaces, "xs") map-get(var.$spaces, "s");
  display: flex;
  align-items: center;
  position: relative;

  &:hover {
    background-color: var.$color-lighter-gray;

    .message-actions {
      display: block;
    }
  }

  .message-actions {
    position: absolute;
    display: none;
    top: 0;
    right: 0;
    padding: map-get(var.$spaces, "xs") map-get(var.$spaces, "s");
    z-index: 10;
  }

  .message-user-photo {
    height: 40px;
    width: 40px;
    align-self: flex-start;
  }

  .message-title {
    font-weight: bold;

    .message-date {
      font-weight: normal;
      font-size: 12px;
    }
  }

  .message-content {
    padding-left: map-get(var.$spaces, "xs");
  }
}

.tropgood {
  display: flex;
}
</style>
