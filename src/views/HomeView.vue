<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import { client } from "../services/contentful";
import { Rive, Layout, Fit, Alignment } from "@rive-app/canvas";

const animation = ref(null);
const riveCanvas = ref(null);

onMounted(async () => {
  try {
    const entry = await client.getEntry("22nHSGqRvwzLgM1KNpytRG");
    const riveFileUrl = entry.fields.riveFile.fields.file.url;
    console.log("Rive file URL:", riveFileUrl); // Log the Rive file URL

    const canvas = riveCanvas.value;

    if (!canvas) {
      console.error("Canvas element not found");
      return;
    }

    // Set canvas size explicitly
    canvas.width = canvas.clientWidth;
    canvas.height = canvas.clientHeight;

    animation.value = new Rive({
      src: riveFileUrl,
      canvas: canvas,
      autoplay: true,
      layout: new Layout({
        fit: Fit.Cover,
        alignment: Alignment.Center,
      }),
      onLoad: () => {
        console.log("Rive animation loaded successfully");
      },
      onError: (error) => {
        console.error("Error loading Rive animation:", error);
      },
    });

    console.log("Rive animation initialized:", animation.value);
  } catch (error) {
    console.error("Error loading Rive animation:", error);
  }
});
</script>

<template>
  <main>
    <canvas ref="riveCanvas" id="rive-canvas"></canvas>
  </main>
</template>

<style scoped>
#rive-canvas {
  width: 100%;
  aspect-ratio: 1150 / 800;
  display: block;
}
</style>
