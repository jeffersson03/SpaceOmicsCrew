<template>
    <div class="card" style="padding:1rem;">
        <h3 style="margin-top:0;color:var(--accent1)">Atributos principales</h3>
        <svg :width="size" :height="size" :viewBox="`0 0 ${size} ${size}`" role="img">
            <g :transform="`translate(${center},${center})`">
                <!-- anillos -->
                <g v-for="r in rings" :key="r">
                    <polygon :points="polygonPoints(r)" fill="none" stroke="rgba(255,255,255,.15)" stroke-width="1" />
                </g>
                <!-- ejes + etiquetas -->
                <g v-for="(k, i) in keys" :key="k">
                    <line :x1="0" :y1="0" :x2="axisPoint(i).x" :y2="axisPoint(i).y" stroke="rgba(255,255,255,.25)"
                        stroke-width="1" />
                    <text :x="labelPoint(i).x" :y="labelPoint(i).y" font-size="12" text-anchor="middle"
                        fill="var(--text)">
                        {{ labels[k] || k }}
                    </text>
                </g>
                <!-- polígono de datos -->
                <polygon :points="dataPoints" fill="url(#grad)" stroke="var(--bg3)" stroke-width="2"
                    fill-opacity=".35" />
                <defs>
                    <radialGradient id="grad">
                        <stop offset="0%" stop-color="#ffffff" stop-opacity="0.2" />
                        <stop offset="100%" stop-color="#B799FF" stop-opacity="0.6" />
                    </radialGradient>
                </defs>
            </g>
        </svg>
    </div>
</template>

<script>
import { computed, defineComponent } from 'vue'

export default defineComponent({
    name: 'RadarGraph',
    props: {
        size: { type: Number, default: 360 },
        values: { type: Object, default: () => ({}) },
        labels: { type: Object, default: () => ({}) }
    },
    setup(props) {
        const keys = computed(() => Object.keys(props.values))
        const center = computed(() => props.size / 2)
        const radius = computed(() => props.size * 0.35)
        const rings = computed(() => [0.25, 0.5, 0.75, 1])

        function angle(i) {
            return (Math.PI * 2 * (i / (keys.value.length || 1))) - Math.PI / 2
        }
        function axisPoint(i) {
            const a = angle(i); return { x: Math.cos(a) * radius.value, y: Math.sin(a) * radius.value }
        }
        function labelPoint(i) {
            const a = angle(i); return { x: Math.cos(a) * (radius.value + 22), y: Math.sin(a) * (radius.value + 22) }
        }
        function toPoint(k, i) {
            const v = Number(props.values[k]) || 0
            const max = Math.max(1, ...Object.values(props.values).map(n => Number(n) || 0))
            const r = radius.value * (v / max)
            const a = angle(i)
            return { x: Math.cos(a) * r, y: Math.sin(a) * r }
        }
        const dataPoints = computed(() =>
            keys.value.map((k, i) => { const p = toPoint(k, i); return `${p.x},${p.y}` }).join(' ')
        )
        function polygonPoints(scale) {
            return keys.value.map((_, i) => {
                const a = angle(i); const r = radius.value * scale
                return `${Math.cos(a) * r},${Math.sin(a) * r}`
            }).join(' ')
        }

        return { size: props.size, labels: props.labels, keys, center, rings, axisPoint, labelPoint, dataPoints, polygonPoints }
    }
})
</script>