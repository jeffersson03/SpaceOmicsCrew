<template>
  <section class="hero" aria-labelledby="hero-title">
    <video
      v-if="videoSource"
      class="hero__video"
      autoplay
      loop
      muted
      playsinline
      preload="metadata"
      :poster="posterSource || undefined"
      :style="{ opacity: videoOpacity }"
    >
      <source :src="videoSource" type="video/mp4" />
    </video>
    <div v-else class="hero__video hero__video--fallback" :style="fallbackStyle" role="presentation"></div>
    <div class="hero__nebula"></div>
    <div class="hero__overlay"></div>
    <div class="hero__content">
      <img v-if="logoSrc" class="hero__logo" :src="logoSrc" alt="Logo Space Omics Crew" />
      <transition name="hero-line" appear>
        <h1 v-if="showHeadline" id="hero-title">Hello, we are Space Omics Crew</h1>
      </transition>
      <transition name="hero-line" appear>
        <p v-if="showSubtitle" class="hero__subtitle">
          This is a repository dedicated to plants with the ability to be resilient
        </p>
      </transition>
      <button class="btn btn-primary hero__cta" type="button" @click="$emit('explore')">
        Explore the radar atlas
        <span class="hero__cta-glow" />
      </button>
    </div>
    <img v-if="mascotSrc" class="hero__mascot" :src="mascotSrc" alt="Space Omics mascot looking forward" />
    <button class="hero__scroll" type="button" @click="$emit('explore')">
      <span>Scroll to explore</span>
      <span class="hero__scroll-indicator" />
    </button>
  </section>
</template>

<script setup>
import { computed, onMounted, ref } from 'vue'
import { resolveAsset } from '../utils/assets'

const props = defineProps({
  video: {
    type: String,
    default: 'images/galaxy.mp4'
  },
  poster: {
    type: String,
    default: 'images/galaxy-poster.svg'
  },
  mascot: {
    type: String,
    default: 'images/mascot-astro.svg'
  },
  logo: {
    type: String,
    default: 'images/logo-space.svg'
  },
  videoOpacity: {
    type: Number,
    default: 0.32
  }
})

defineEmits(['explore'])

const showHeadline = ref(false)
const showSubtitle = ref(false)

onMounted(() => {
  requestAnimationFrame(() => {
    showHeadline.value = true
    window.setTimeout(() => {
      showSubtitle.value = true
    }, 900)
  })
})

const logoSrc = computed(() => resolveAsset(props.logo, { fallback: resolveAsset('images/logo-space.svg') }))
const mascotSrc = computed(() => resolveAsset(props.mascot, { fallback: resolveAsset('images/mascot-astro.svg') }))
const videoSource = computed(() => {
  const resolved = resolveAsset(props.video, { fallback: null })
  if (!resolved) return null
  return /\.(mp4|webm|ogg)(\?|$)/i.test(resolved) ? resolved : null
})
const posterSource = computed(() => resolveAsset(props.poster, { fallback: null }))
const fallbackStyle = computed(() => ({
  opacity: props.videoOpacity,
  backgroundImage: posterSource.value ? `url(${posterSource.value})` : undefined
}))
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
  pointer-events: none;
  filter: saturate(1.05) brightness(0.65);
}

.hero__video--fallback {
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  filter: saturate(1.05) brightness(0.65);
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
  opacity: 0;
  animation: hero-fade 1.4s ease forwards 0.2s, float 9s ease-in-out infinite 1.6s;
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
  pointer-events: none;
  opacity: 0;
  animation: hero-fade 1.6s ease forwards 0.6s, float 7s ease-in-out infinite 1.8s;
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

@keyframes hero-fade {
  0% {
    opacity: 0;
    transform: translateY(24px) scale(0.98);
  }
  60% {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
  100% {
    opacity: 1;
    transform: translateY(0) scale(1);
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

.hero-line-enter-active {
  transition: opacity 0.8s ease, transform 0.8s ease;
}

.hero-line-enter-from {
  opacity: 0;
  transform: translateY(16px);
}

.hero-line-enter-to {
  opacity: 1;
  transform: translateY(0);
}

@media (prefers-reduced-motion: reduce) {
  .hero__logo,
  .hero__mascot,
  .hero__cta,
  .hero-line-enter-active {
    animation: none !important;
    transition: none !important;
  }
  .hero__logo,
  .hero__mascot {
    opacity: 1;
  }
}
</style>
