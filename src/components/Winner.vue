<template>
  <template v-if="pause && !winner">
    <div class="absolute">
      <div class="flex-column">
        <h1>Pause</h1>
        <div class="top-12 flex-center">
          <RouterLink to="/">
            <Btn text="Menu" />
          </RouterLink>
        </div>
      </div>
    </div>
  </template>

  <template v-if="win">
    <div class="absolute">
      <div class="flex-column">
        <h1>{{ winner }} wins!</h1>
        <div class="top-12 flex-center">
          <RouterLink to="/">
            <Btn text="Menu" />
          </RouterLink>
        </div>
      </div>
    </div>
  </template>

  <template v-if="timeout">
      <div class="absolute">
        <div class="flex-column">
          <h1>It's a tie</h1>
          <div class="top-12 flex-center">
            <RouterLink to="/">
              <Btn text="Menu" />
            </RouterLink>
          </div>
        </div>
      </div>
  </template>
</template>

<script setup>
// ==============================
// Import
// ==============================
import Btn from "../components/Btn.vue";
import { Store } from "@/stores/store";
import { onMounted, onUnmounted, ref, watch } from "@vue/runtime-core";
import { RouterLink, RouterView } from "vue-router";

// ==============================
// Props
// ==============================
const props = defineProps({
  winner: String,
})

// ==============================
// Variables
// ==============================
const store = Store();
const pause = ref( false );
const timeout = ref( false );
const win = ref( undefined ); 

// ==============================
// Watcher
// ==============================

// Watch timeout
watch(
  () => store.getFightTime,
  ( time ) => {
    timeout.value = ( time == "timeout" )? true : false
    document.removeEventListener("keydown", handleKeyDown);
  }
);

// Watch winner
watch(
  () => props.winner,
  ( player ) => {
    win.value = player;
    document.removeEventListener("keydown", handleKeyDown);
  }
);

// Watch pause mode
watch(
  () => store.getPauseMode,
  ( isPaused ) => pause.value = isPaused
);

function handleKeyDown(event) {
  if ( event.key == store.getPauseKey ) {
    store.setTogglePause()
  }
}
// ==============================
// Events
// ==============================
onMounted(() => {
  document.addEventListener("keydown", handleKeyDown);
});

// Remove event listener to prevent multiple listening and multiple execution of handleKeyDown()
onUnmounted(() => {
  document.removeEventListener("keydown", handleKeyDown);
});
</script>

<style lang="scss" scoped>
.absolute {
  z-index: 20;
  position: absolute;
  width: 100%;
  height: 100vh;
  left: 0;
  top: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(0, 0, 0, 0.6);
  h1 {
    font-size: 50px;
  }
}
</style>
