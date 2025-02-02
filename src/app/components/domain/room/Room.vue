<script lang="ts" setup>
import Message from "@/app/components/domain/message/Message.vue";
import { useProvider, useState, useStore } from "@/app/platform";
import { MessageService } from "@/modules/message/services/MessageService";
import { RoomSocketService } from "@/modules/room/services";
import { MessageStore } from "@/modules/message/store";
import { watch, ref } from "vue";
import { useIntersectionObserver } from "@vueuse/core";
import { MessageSocketService } from "@/modules/message/services/MessageSocketService";
import { AuthenticationStore } from "@/modules/authentication/store/AuthenticationStore";
import { ElNotification } from "element-plus";
import type { Room } from "@/modules/room/models/domain/Room";
import type { RichMessage } from "@/modules/message/models/domain";

const props = defineProps<{ room: Room }>();
const state = useState(MessageStore);
const authState = useState(AuthenticationStore);
const store = useStore(MessageStore);

const [messageSerivce, messageSocket, roomSocket] = useProvider([
  MessageService,
  MessageSocketService,
  RoomSocketService
]);

const loading = ref(false);

// Element used to know if we should load more message
const top = ref<HTMLDivElement | null>(null);

// Should be use to know the size in px of all the messages displayed
const container = ref<HTMLDivElement | null>(null);

// Element that have the scrollbar
const root = ref<HTMLDivElement | null>(null);

subscribeToIncomingMessage();


watch(
  () => props.room,
  async () => {
    /**
     * Each time the room changes, fetch messages and subscribe to new messages
     */

    store.reset();
    await fetchMore();

    subscribeToIncomingMessage();
  }
);
function compare(a: RichMessage, b: RichMessage): boolean {
  for (let i = 0; i < a.text.tokens.length; i++) {
    if (a.text.tokens[i].value != b.text.tokens[i].value) {
      return false
    }
  }
  return true;
}
function subscribeToIncomingMessage() {
  messageSocket.onNewMessage(props.room.id, async (post) => {
    if (post.author.id != authState.loggedUser?.id) {
      //await messageSerivce.sendMessage(post);
      const parsed = messageSerivce.parseMessage(post);
      store.state.currentRoomMessages.unshift(parsed);
      //state.currentRoomMessages = store.state.currentRoomMessages.slice().reverse();
    }

  });
}

async function fetchMore() {
  if (loading.value) {
    return;
  }

  try {
    loading.value = true;
    await messageSerivce.fetchMore(props.room.id);

    // TODO fetch more messages
  } catch (e) {
    console.error(e);
  } finally {
    loading.value = false;
  }
}
</script>

<template>
  <div class="room stretch-wh" ref="root">
    <div class="room-container" ref="container">
      <div ref="top">
        <Message v-for="message, index in store.state.currentRoomMessages.slice().reverse()" :message="message"
          :key="index">
        </Message>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.room {
  display: flex;
  flex-direction: column-reverse;
  overflow-y: auto;
}

.room-container {
  display: flex;
  flex-direction: column-reverse;
}
</style>
