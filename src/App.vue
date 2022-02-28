<template>
  <div
    class="min-h-screen px-6 py-12 md:p-12 flex flex-col"
    tabindex="0"
    @keydown="play"
  >
    <header class="flex flex-row justify-between items-center">
      <div class="flex flex-col space-y-2">
        <h1>Sticky Board</h1>
        <h2>Retro sounds in the modern age</h2>
      </div>
    </header>
    <main class="flex-grow flex flex-col justify-center space-y-4">
      <div class="flex flex-col">
        <h1 @click="this.active = true">{{ instructions }}</h1>
        <p :class="[status ? 'text-emerald-500' : 'text-red-500']">
          Sticky keys are {{ status ? "on" : "off" }}
        </p>
      </div>
      <div class="md:hidden flex flex-row justify-between">
        <button @click="toggle">Turn {{ status ? "Off" : "On" }}</button>
        <button @click="beep1">Beep 1</button>
        <button @click="beep2">Beep 2</button>
      </div>
    </main>
    <footer
      class="text-sm flex flex-col md:flex-row space-y-2 md:space-y-0 md:justify-between"
    >
      <p>
        Not affilcated with Microsoft. I made this for fun and nostalgic reasons
      </p>
      <a
        href="https://github.com/dgrinbergs/sticky-board"
        target="_blank"
        class="underline"
        >Github</a
      >
    </footer>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

type action = {
  key: string;
  sound: string;
  predicate: (status: boolean) => boolean;
  action: (status: boolean) => boolean;
};

export default defineComponent({
  name: "App",
  data() {
    return {
      actions: [
        {
          key: "enter",
          sound: require("./assets/sounds/shift.mp3"),
          predicate: (status: boolean) => !status,
          action: (status: boolean) => !status,
        },
        {
          key: "backspace",
          sound: require("./assets/sounds/off.mp3"),
          predicate: (status: boolean) => status,
          action: (status: boolean) => !status,
        },
        {
          key: "1",
          sound: require("./assets/sounds/beep1.mp3"),
          predicate: (status: boolean) => status,
          action: (status: boolean) => status,
        },
        {
          key: "2",
          sound: require("./assets/sounds/beep2.mp3"),
          predicate: (status: boolean) => status,
          action: (status: boolean) => status,
        },
      ],
      active: false,
      status: false,
    };
  },
  computed: {
    instructions() {
      if (!this.active) {
        return "Click me!";
      }
      if (this.status) {
        return "Use '1' and '2' to beep and 'backspace' to turn sticky keys off.";
      } else {
        return `Press 'enter' to turn on sticky keys.`;
      }
    },
  },
  methods: {
    play({ key, repeat }: { key: string; repeat: boolean }): void {
      if (repeat) return;

      key = key.toLowerCase();

      const { sound, predicate, action } =
        this.actions.filter((action: action) => action.key === key)[0] || {};

      if (sound && predicate(this.status)) {
        const audio = new Audio(sound);
        audio.play();
        this.status = action(this.status);
      }
    },
    toggle() {
      const key = this.status ? "backspace" : "enter";
      return this.play({ key, repeat: false });
    },
    beep1() {
      this.play({ key: "1", repeat: false });
    },
    beep2() {
      this.play({ key: "2", repeat: false });
    },
  },
});
</script>
