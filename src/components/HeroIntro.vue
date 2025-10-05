<template>
  <section class="hero" aria-labelledby="hero-title">
    <video
      class="hero__video"
      autoplay
      loop
      muted
      playsinline
      preload="metadata"
      :style="{ opacity: videoOpacity }"
    >
      <source :src="videoSource" type="video/mp4" />
    </video>
    <div class="hero__nebula"></div>
    <div class="hero__overlay"></div>
    <div class="hero__content">
      <img class="hero__logo" :src="logoSrc" alt="Logo Space Omics Crew" />
      <h1 id="hero-title">Hello, we are Space Omics Crew</h1>
      <p class="hero__subtitle">
        This is a repository dedicated to plants with the ability to be resilient
      </p>
      <button class="btn btn-primary hero__cta" type="button" @click="$emit('explore')">
        Explore the radar atlas
        <span class="hero__cta-glow" />
      </button>
    </div>
    <img class="hero__mascot" :src="mascotSrc" alt="Space Omics mascot" />
    <button class="hero__scroll" type="button" @click="$emit('explore')">
      <span>Scroll to explore</span>
      <span class="hero__scroll-indicator" />
    </button>
  </section>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  video: {
    type: String,
    default: '/assets/images/galaxy.mp4'
  },
  videoOpacity: {
    type: Number,
    default: 0.28
  }
})

defineEmits(['explore'])

const logoSrc = new URL('../assets/images/logo-space.svg', import.meta.url).href
const mascotSrc = new URL('../assets/images/mascot-astro.svg', import.meta.url).href

const videoSource = computed(() => props.video)
</script>

<style scoped>
.hero {
  position: relative;
  min-height: 88vh;
  display: grid;
  place-items: center;
  overflow: hidden;
  border-bottom: 1px solid rgba(198, 185, 255, 0.2);
  background: radial-gradient(circle at 20% 20%, rgba(123, 90, 248, 0.35), transparent 55%),
    radial-gradient(circle at 80% 30%, rgba(127, 184, 166, 0.25), transparent 45%),
    #050716;
}

.hero__video {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  mix-blend-mode: screen;
  pointer-events: none;
}

.hero__overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(160deg, rgba(7, 12, 36, 0.9) 20%, rgba(11, 16, 38, 0.4) 60%, rgba(7, 11, 32, 0.9) 90%);
  pointer-events: none;
}

.hero__nebula {
  position: absolute;
  inset: 0;
  pointer-events: none;
}

.hero__nebula::before,
.hero__nebula::after {
  content: '';
  position: absolute;
  width: 600px;
  height: 600px;
  border-radius: 50%;
  filter: blur(120px);
  animation: drift 16s ease-in-out infinite;
}

.hero__nebula::before {
  top: -120px;
  left: -160px;
  background: rgba(198, 185, 255, 0.35);
}

.hero__nebula::after {
  bottom: -140px;
  right: -180px;
  background: rgba(127, 184, 166, 0.25);
  animation-delay: 6s;
}

.hero__content {
  position: relative;
  text-align: center;
  padding: 2.5rem 1.5rem;
  max-width: 720px;
  z-index: 2;
}

.hero__logo {
  width: clamp(160px, 25vw, 220px);
  margin-bottom: 1.75rem;
  filter: drop-shadow(0 0 18px rgba(198, 185, 255, 0.45));
  animation: float 9s ease-in-out infinite;
}

.hero__content h1 {
  font-size: clamp(2.5rem, 6vw, 3.7rem);
  margin: 0 0 1rem;
  font-weight: 800;
  letter-spacing: 0.02em;
  background: linear-gradient(135deg, var(--accent1), var(--bg3), var(--accent2));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-shadow: 0 0 28px rgba(198, 185, 255, 0.25);
}

.hero__subtitle {
  margin: 0 auto 2rem;
  font-size: 1.2rem;
  line-height: 1.7;
  max-width: 560px;
  color: rgba(244, 203, 234, 0.9);
}

.hero__cta {
  position: relative;
  padding-inline: 1.75rem;
  font-size: 1rem;
  box-shadow: 0 10px 40px rgba(122, 90, 248, 0.4);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.hero__cta:hover {
  transform: translateY(-4px) scale(1.02);
  box-shadow: 0 14px 60px rgba(127, 184, 166, 0.4);
}

.hero__cta-glow {
  position: absolute;
  inset: -40% -120%;
  background: radial-gradient(circle, rgba(198, 185, 255, 0.6), transparent 55%);
  opacity: 0;
  transition: opacity 0.4s ease;
  filter: blur(12px);
}

.hero__cta:hover .hero__cta-glow {
  opacity: 1;
}

.hero__mascot {
  position: absolute;
  bottom: -1.5rem;
  right: clamp(3%, 8vw, 12%);
  width: clamp(180px, 28vw, 280px);
  z-index: 1;
  animation: float 7s ease-in-out infinite;
  pointer-events: none;
}

.hero__scroll {
  position: absolute;
  bottom: 2rem;
  left: 50%;
  transform: translateX(-50%);
  display: inline-flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.6rem 1.2rem;
  background: rgba(11, 16, 38, 0.55);
  border: 1px solid rgba(198, 185, 255, 0.3);
  border-radius: 999px;
  color: rgba(244, 203, 234, 0.85);
  font-size: 0.95rem;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  cursor: pointer;
  z-index: 2;
  transition: transform 0.3s ease, background 0.3s ease;
}

.hero__scroll:hover {
  transform: translate(-50%, -6px);
  background: rgba(11, 16, 38, 0.8);
}

.hero__scroll-indicator {
  position: relative;
  width: 12px;
  height: 24px;
  border-radius: 999px;
  border: 2px solid rgba(198, 185, 255, 0.6);
}

.hero__scroll-indicator::after {
  content: '';
  position: absolute;
  top: 4px;
  left: 50%;
  width: 4px;
  height: 4px;
  border-radius: 50%;
  background: var(--accent1);
  transform: translateX(-50%);
  animation: pulse 1.8s ease-in-out infinite;
}

@keyframes float {
  0%,
  100% {
    transform: translateY(0px);
  }
  50% {
    transform: translateY(-12px);
  }
}

@keyframes drift {
  0%,
  100% {
    transform: scale(1) translate(0, 0);
  }
  50% {
    transform: scale(1.15) translate(20px, -18px);
  }
}

@keyframes pulse {
  0% {
    opacity: 0.2;
    transform: translate(-50%, 0);
  }
  50% {
    opacity: 1;
    transform: translate(-50%, 6px);
  }
  100% {
    opacity: 0.2;
    transform: translate(-50%, 0);
  }
}

@media (max-width: 820px) {
  .hero {
    min-height: 100vh;
    padding-top: 5rem;
  }

  .hero__mascot {
    position: static;
    width: 220px;
    margin-top: 2rem;
  }

  .hero__scroll {
    bottom: 1.5rem;
  }
}
</style>
