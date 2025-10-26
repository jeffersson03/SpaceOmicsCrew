<template>
  <section class="container" style="padding-top:1rem;">
    <div style="text-align:center;margin-bottom:1rem;">
      <h1
        style="font-size:3rem;font-weight:900;background:linear-gradient(135deg,var(--accent1),var(--bg3),var(--accent2));-webkit-background-clip:text;-webkit-text-fill-color:transparent;">
        Plant Resilience Repository
      </h1>
      <p class="muted">Carrusel 3D con zoom + grafo de atributos de la planta seleccionada.</p>
    </div>

    <PlantCarousel :especies="especies" @select="onSelect" @verRepositorio="verRepo" />

    <div v-if="seleccion" class="grid" style="grid-template-columns: 1.1fr .9fr; margin-top:1rem;">
      <div class="card">
        <h2 style="margin-top:0;color:var(--accent1)">{{ seleccion.nombre_cientifico }}</h2>
        <div class="muted">{{ seleccion.nombre_comun }} • {{ seleccion.familia }} • {{ seleccion.via_fotosintesis }}
        </div>
        <div style="display:flex;gap:1rem;align-items:center;margin-top:1rem;flex-wrap:wrap;">
          <router-link class="btn btn-primary" :to="`/repositorio/${seleccion.especie_id}`">Abrir
            repositorio</router-link>
          <button class="btn btn-secondary" @click="irAFichaPrimera">Ver 1er experimento</button>
        </div>

        <PlantHotspots v-if="seleccion.imagen_url" :img="seleccion.imagen_url" :parts="hotspotParts" />
      </div>

      <RadarGraph :values="radarValues" :labels="radarLabels" :size="360" :palette="seleccionPalette" />
    </div>

    <section v-if="galleryReady" class="gallery-section">
      <h2>Mapas radiales interactivos por cultivo</h2>
      <p class="muted">Visualiza cada especie con su holograma 3D y radar dinámico.</p>
      <PlantRadarGallery :species="especies" :experiments="experimentos" :results="resultados" :labels="radarLabels" />
    </section>
  </section>
</template>

<script setup>
import { computed, ref, onMounted } from 'vue'
import PlantCarousel from '../components/PlantCarousel.vue'
import RadarGraph from '../components/RadarGraph.vue'
import PlantHotspots from '../components/PlantHotspots.vue'
import PlantRadarGallery from '../components/PlantRadarGallery.vue'
import { getEspecies, getExperimentos, getResultados } from '../services/api'
import { plantThemes, defaultPlantTheme } from '../data/plantThemes'

const especies = ref([])
const seleccion = ref(null)
const radarValues = ref({})
const experimentos = ref([])
const resultados = ref([])
const radarLabels = {
  rendimiento: 'Rendimiento (g/m²/d)',
  dias: 'Días a cosecha',
  ph: 'pH',
  ec: 'EC (mS/cm)',
  co2: 'CO₂ (ppm)'
}

const hotspotParts = ref([
  { id: 'hoja', label: 'Hoja', x: 0.7, y: 0.35 },
  { id: 'tallo', label: 'Tallo', x: 0.5, y: 0.55 },
  { id: 'raiz', label: 'Raíz', x: 0.5, y: 0.85 },
  { id: 'fruto', label: 'Fruto', x: 0.45, y: 0.25 }
])

const seleccionPalette = computed(() => {
  if (!seleccion.value) return defaultPlantTheme.palette
  return (plantThemes[seleccion.value.nombre_comun] || plantThemes[seleccion.value.nombre_cientifico] || defaultPlantTheme).palette
})

const galleryReady = computed(() => especies.value.length && experimentos.value.length && resultados.value.length)

function onSelect(esp) {
  seleccion.value = esp
  cargarRadar(esp.especie_id)
}

function cargarRadar(especieId) {
  const e = experimentos.value.find(x => x.especie_id === especieId)
  const r = e ? resultados.value.find(x => x.experimento_id === e.experimento_id) : null
  radarValues.value = {
    rendimiento: r?.rendimiento_g_m2_d || 0,
    dias: r?.dias_cosecha || 0,
    ph: e?.env_ph || 0,
    ec: e?.env_ec_conductividad || 0,
    co2: e?.env_co2_ppm || 0
  }
}

function verRepo(esp) { window.location.hash = `#/repositorio/${esp.especie_id}` }
async function irAFichaPrimera() {
  if (!seleccion.value) return
  if (!experimentos.value.length) experimentos.value = await getExperimentos()
  const e = experimentos.value.find(x => x.especie_id === seleccion.value.especie_id)
  if (e) window.location.hash = `#/experimento/${e.experimento_id}`
}

onMounted(async () => {
  const [esp, exp, res] = await Promise.all([getEspecies(), getExperimentos(), getResultados()])
  especies.value = esp
  experimentos.value = exp
  resultados.value = res
  if (esp.length) {
    seleccion.value = esp[0]
    cargarRadar(esp[0].especie_id)
  }
})
</script>

<style scoped>
.gallery-section {
  margin-top: 3rem;
}

.gallery-section h2 {
  margin-bottom: 0.35rem;
  color: var(--accent1);
  font-weight: 800;
}

.gallery-section p {
  margin-top: 0;
  margin-bottom: 1.5rem;
}
</style>
