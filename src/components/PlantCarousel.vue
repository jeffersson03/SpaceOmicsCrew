<template>
    <div class="carousel-wrap">
        <button class="nav left" @click="prev">‹</button>
        <div class="carousel">
            <div v-for="(s, i) in especies" :key="s.especie_id" class="item" :class="{ center: i === idx }"
                :style="itemStyle(i)" @click="centerOn(i)">
                <div class="thumb" :style="thumbStyle(s)"></div>
                <div class="label">
                    <strong>{{ s.nombre_cientifico }}</strong>
                    <span class="muted">{{ s.nombre_comun }}</span>
                </div>
                <button class="btn btn-secondary small" @click.stop="$emit('verRepositorio', s)">Ver
                    repositorio</button>
            </div>
        </div>
        <button class="nav right" @click="next">›</button>
    </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
const props = defineProps({ especies: Array, autoRotateMs: { type: Number, default: 5000 } })
const emit = defineEmits(['select', 'verRepositorio'])
const idx = ref(0)
let timer = null
function next() {
    idx.value = (idx.value + 1) % props.especies.length
    emit('select', props.especies[idx.value])
}
function prev() {
    idx.value = (idx.value - 1 + props.especies.length) % props.especies.length
    emit('select', props.especies[idx.value])
}
function centerOn(i) { idx.value = i; emit('select', props.especies[idx.value]) }
function itemStyle(i) {
    const offset = i - idx.value
    const depth = Math.max(0, 1 - Math.abs(offset) * 0.2)
    const z = 100 - Math.abs(offset)
    const translateX = offset * 180
    const rotateY = offset * -25
    const scale = i === idx.value ? 1.1 : 0.9 * depth
    return { transform: `translateX(${translateX}px) rotateY(${rotateY}deg) scale(${scale})`, zIndex: z }
}
function thumbStyle(s) {
    const url = s.imagen_url || '/src/assets/placeholder-plant.svg'
    return { backgroundImage: `url('${url}')` }
}
onMounted(() => { if (props.especies?.length) { emit('select', props.especies[0]); if (props.autoRotateMs > 0 && props.especies.length > 1) { timer = setInterval(() => next(), props.autoRotateMs) } } })
onBeforeUnmount(() => { if (timer) clearInterval(timer) })
</script>

<style scoped>
.carousel-wrap {
    position: relative;
    perspective: 1200px;
    height: 360px;
    display: flex;
    align-items: center;
    justify-content: center;
}

.carousel {
    position: relative;
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
}

.item {
    position: absolute;
    top: 40px;
    width: 300px;
    padding: 14px;
    border-radius: 18px;
    border: 1px solid rgba(198, 185, 255, .2);
    background: rgba(198, 185, 255, .08);
    backdrop-filter: blur(12px);
    transition: transform .6s ease, filter .3s ease, opacity .3s ease;
    box-shadow: 0 10px 30px rgba(0, 0, 0, .25);
    opacity: .8
}

.item.center {
    opacity: 1;
}

.thumb {
    width: 100%;
    height: 180px;
    border-radius: 14px;
    background-size: contain;
    background-position: center;
    background-repeat: no-repeat;
    background-color: rgba(16, 20, 51, .9);
}

.label {
    margin-top: .6rem;
    display: flex;
    flex-direction: column;
    gap: .15rem;
    color: var(--accent1);
}

.small {
    padding: .45rem .8rem;
    font-size: .85rem;
    margin-top: .4rem;
}

.nav {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    border: 0;
    width: 42px;
    height: 42px;
    border-radius: 50%;
    cursor: pointer;
    background: linear-gradient(135deg, var(--accent1), var(--bg3));
    color: #0B1026;
    font-size: 1.6rem;
    font-weight: 800;
}

.nav.left {
    left: 10px
}

.nav.right {
    right: 10px
}
</style>