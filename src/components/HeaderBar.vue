
<template>
  <header class="sticky">
    <div class="container" style="display:flex;align-items:center;justify-content:space-between;gap:1rem;">
      <router-link to="/" style="display:flex;align-items:center;gap:.6rem;font-weight:800;color:var(--accent1)">
        <div style="width:28px;height:28px;border-radius:8px;background:linear-gradient(135deg,var(--accent1),var(--accent2));"></div>
        <span>Space Omics</span>
      </router-link>
      <nav style="display:flex;gap:1rem;">
        <router-link to="/" class="pill">Inicio</router-link>
        <router-link to="/comparador" class="pill">Comparador</router-link>
        <router-link to="/mapeo" class="pill">Mapeo de Datos</router-link>
      </nav>
    </div>
  </header>
</template>
<script setup>
</script>
