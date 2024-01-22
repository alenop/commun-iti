<script lang="ts" setup>
import RichText from "@/app/components/ui/RichText.vue";
import BgImage from "../../ui/BgImage.vue";
import ItiEmojiPicker from "../../ui/emoji-picker/EmojiPicker.vue";
import EmojiIcon from "../../ui/icons/EmojiIcon.vue";
import MessageAttachements from "./MessageAttachements.vue";
import { useProvider } from "@/app/platform";
import { MessageService } from "@/modules/message/services/MessageService";
import { DateTime } from "luxon";
import { useStore } from "@/app/platform";
import { MessageStore } from "@/modules/message/store";
import { RoomStore } from "@/modules/room/store";
import { MessageAPI } from "@/modules/message/services/MessageAPI";
import { RoomAPI } from "@/modules/room/services";

async function getRich() {
  console.log("get Rich");
  const msgStore = useStore(MessageStore);
  const store = useStore(RoomStore);
  if (store.state.currentRoom != null) {
    await messageSerivce.fetchMore(store.state.currentRoom.id);
    //return msgStore.state.currentRoomMessages;
    const te = await apiService.fetch(store.state.currentRoom.id, { page: 1, perPage: 1 });
    return te.data[te.data.length - 1].text
    //return msgStore.state.currentRoomMessages[msgStore.state.currentRoomMessages.length-1];
  }
} import MessageReactions, { type MessageReaction } from "./MessageReactions.vue";
import { type Message } from "@/modules/message/models/domain";

const props = defineProps<{
  message: Message;
}>();

const [messageSerivce] = useProvider([MessageService]);

</script>

<template>
  <div class="message">
    <div class="message-actions">
      <iti-emoji-picker ref="emojiPicker" @pick="onEmojiPicked" />
      <el-button :icon="EmojiIcon" circle size="small" @click="$refs.emojiPicker.show()" />
    </div>

    <bg-image class="message-user-photo" src="" />

    <div class="message-content">
      <div class="message-title">
        <small class="message-date"></small>
        <rich-text :text="message.text"></rich-text>
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
</style>
