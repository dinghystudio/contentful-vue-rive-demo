<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import { client } from "../services/contentful";
import { Rive, Layout, Fit, Alignment, EventType } from "@rive-app/canvas";

const animation = ref(null);
const riveCanvas = ref(null);

const handleRiveEvent = (riveEvent) => {
  if (riveEvent.data && riveEvent.data.name) {
    if (riveEvent.data.name === "hover") {
      console.log("Hover event detected");
      riveCanvas.value.style.cursor = "pointer";
    } else if (riveEvent.data.name === "unhover") {
      console.log("Unhover event detected");
      riveCanvas.value.style.cursor = "default";
    }
  }
};

onMounted(async () => {
  try {
    const entry = await client.getEntry("22nHSGqRvwzLgM1KNpytRG");
    const riveFileUrl = entry.fields.riveFile.fields.file.url;
    console.log("Rive file URL:", riveFileUrl);

    const canvas = riveCanvas.value;
    if (!canvas) {
      console.error("Canvas element not found");
      return;
    }

    // Fix blurriness by adjusting for device pixel ratio
    const dpr = window.devicePixelRatio || 1;
    canvas.width = canvas.clientWidth * dpr;
    canvas.height = canvas.clientHeight * dpr;

    // Ensure the CSS size stays the same (prevents scaling issues)
    canvas.style.width = `${canvas.clientWidth}px`;
    canvas.style.height = `${canvas.clientHeight}px`;

    animation.value = new Rive({
      src: riveFileUrl,
      canvas: canvas,
      autoplay: true,
      stateMachines: "MainStateMachine",
      layout: new Layout({
        fit: Fit.Cover,
        alignment: Alignment.Center,
      }),
      onLoad: () => {
        console.log("Rive animation loaded successfully");

        // Call resize function to properly scale
        animation.value.resizeDrawingSurfaceToCanvas();
        animation.value.startRendering();

        // Listen for Rive Events
        animation.value.on(EventType.RiveEvent, handleRiveEvent);
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

// Cleanup event listeners when the component unmounts
onUnmounted(() => {
  if (animation.value) {
    animation.value.off(EventType.RiveEvent, handleRiveEvent);
    console.log("Removed Rive event listeners");
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
