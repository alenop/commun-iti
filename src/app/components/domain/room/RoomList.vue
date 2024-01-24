<script lang="ts" setup>
import { RoomStore } from "@/modules/room/store";
import { useState, useStore } from "@/app/platform";
import type { Room } from "@/modules/room/models/domain/Room";
import router from "@/app/router";

const state = useState(RoomStore);
const store = useStore(RoomStore);

async function selectRoom(room: Room) {
  store.setCurrentRoom(room);
  await router.push("/app/room/" + room.id);
  const monStockage = localStorage;
  monStockage.setItem("lastRoom", room.id);
}

</script>

<template>
  <nav class="room-list">
    <ul>
      <li class="room-item" @click="selectRoom(a)" v-for=" a of store.state.rooms">
        {{ a.name }}
      </li>
    </ul>
  </nav>
</template>

<style lang="scss" scoped>
@use "sass:map";
@use "@/app/styles/var";

.room-list {
  padding-top: 1px;

  >ul {
    margin: 0;
    list-style-type: none;
    padding-inline: 0;

    >li {
      margin: 0;
      text-decoration: none;
      display: block;

      a {
        color: var.$color-lighter;
        text-decoration: none;
        display: block;
        padding: map-get(var.$spaces, "2xs") map-get(var.$spaces, "xs");

        &:hover {
          background-color: var.$color-light;
        }

        &.router-link-active {
          color: #ffffff;
          font-weight: 500;
        }
      }
    }
  }
}

.room-item {
  color: white;
  padding-left: 10px;
  background-color: #ffffff11;
}

.room-item:hover {
  background-color: #ffffff22;
}
</style>
