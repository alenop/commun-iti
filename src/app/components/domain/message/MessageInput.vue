<script lang="ts" setup>
import { MessageService } from "@/modules/message/services/MessageService";
import { useProvider, useState } from "@/app/platform";
import { RoomStore } from "@/modules/room/store";
import type { RichText } from "@/modules/message/models/domain";
import RichTextEditorVue from "../../ui/RichTextEditor.vue";

const state = useState(RoomStore);

function test(text: RichText) {

  if (state.currentRoom != null) {
    messageService.sendMessage({ text: text, roomId: state.currentRoom.id });
  }

}

const [messageService] = useProvider([MessageService]);
const roomState = useState(RoomStore);
/*onMounted(()=>{
  const monStockage = localStorage;
  let complement = state.rooms[0].id;
  const lastroom = monStockage.getItem("lastRoom");
  if(lastroom!=null){
    complement = lastroom;
  }
  router.push("app/room/"+complement);
})*/
</script>
<template>
  <div class="message-input stretch-wh">
    <RichTextEditorVue @input="test"></RichTextEditorVue>
  </div>
</template>
<style lang="scss" scoped>
@use "@/app/styles/var";

.message-input {
  border-top: 1px solid var.$color-light-gray;
  background-color: var.$color-light-gray;
}
</style>
