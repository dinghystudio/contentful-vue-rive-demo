<script setup>
import { ref, onMounted } from "vue";
import { client } from "../services/contentful";
import { Rive } from "@rive-app/canvas";

const animation = ref(null);
const riveCanvas = ref(null);

onMounted(async () => {
  try {
    const entry = await client.getEntry("22nHSGqRvwzLgM1KNpytRG");
    const riveFileUrl = entry.fields.riveFile.fields.file.url;

    // Use the ref directly instead of this.$refs
    const canvas = riveCanvas.value;

    animation.value = new Rive({
      src: riveFileUrl,
      canvas: canvas,
      autoplay: true,
      onLoad: () => {
        // Note: You'll need to implement resizeCanvas as a separate function
        // or remove this if not needed
        // resizeCanvas();
      },
    });
  } catch (error) {
    console.error("Error loading Rive animation:", error);
  }
});
</script>

<template>
  <main>
    <canvas ref="riveCanvas" width="400" height="400"></canvas>
  </main>
</template>
