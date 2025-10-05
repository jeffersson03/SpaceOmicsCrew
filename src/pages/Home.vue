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

      <RadarGraph :values="radarValues" :labels="radarLabels" :size="360" />
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import PlantCarousel from '../components/PlantCarousel.vue'
import RadarGraph from '../components/RadarGraph.vue'
import PlantHotspots from '../components/PlantHotspots.vue'
import { getEspecies, getExperimentos, getResultados } from '../services/api'

const especies = ref([])
const seleccion = ref(null)
const radarValues = ref({})
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

function onSelect(esp) { seleccion.value = esp; cargarRadar(esp.especie_id) }

async function cargarRadar(especieId) {
  const [exp, res] = await Promise.all([getExperimentos(), getResultados()])
  const e = exp.find(x => x.especie_id === especieId)
  const r = e ? res.find(x => x.experimento_id === e.experimento_id) : null
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
  const exps = await getExperimentos()
  const e = exps.find(x => x.especie_id === seleccion.value.especie_id)
  if (e) window.location.hash = `#/experimento/${e.experimento_id}`
}

onMounted(async () => { especies.value = await getEspecies() })
</script>
