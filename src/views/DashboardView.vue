<template>
  <LayoutDefault layout>
    <v-container>
      <h1 class="text-h4">Bienvenido</h1>
      <v-divider class="my-5 mx-auto"></v-divider>
      <v-card class="pa-2 card-backgoundcustom" id="container" elevation="2">
        <v-card-item>
          <v-card-title class="text-h5"> Clasificación de herramientas </v-card-title>
          <v-card-subtitle>
            Ingresa los modulos que deseas evaluar
          </v-card-subtitle>
        </v-card-item>
        <v-card-text class="pb-0">
          <v-combobox v-model="selected" clearable chips multiple label="Combobox" :items="modulos"></v-combobox>
          <v-divider></v-divider>
          <v-card-actions class="my-2">
            <v-btn @click="consultarRanking()" color="" class="px-3" rounded="sm" variant="text">Procesar</v-btn>
          </v-card-actions>
        </v-card-text>
      </v-card>
      <v-card class=" my-5 pa-2 card-backgoundcustom" id="container" elevation="2"
        v-if="resultado && !loading && !error">
        <v-card-item>
          <v-card-title class="text-h5"> Resultado del ranking </v-card-title>
        </v-card-item>
        <v-card-text class="pb-0">
          <div v-if="loading">Cargando...</div>
          <div v-if="error" class="text-red">{{ error }}</div>
          <template v-if="resultado && typeof resultado === 'object'">
            <h3>Herramientas que cumplen el alcance</h3>
            <v-list class="bg-transparent"
              v-if="resultado['Herramientas que cumplen el alcance'] && resultado['Herramientas que cumplen el alcance'].length">
              <v-list-item v-for="(herramienta, idx) in resultado['Herramientas que cumplen el alcance']" :key="idx">
                <v-list-item-content>
                  <v-list-item-title>{{ herramienta.nombre }}</v-list-item-title>
                  <v-list-item-subtitle>
                    Precio: {{ herramienta.precio }}<br>
                    Módulos que cubre: {{ herramienta.modulos_cubre }}
                  </v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
            </v-list>
            <div v-else>
              Ninguna herramienta cubre todos los módulos seleccionados.
            </div>
            <v-divider class="my-4"></v-divider>
            <h3>Herramientas que no cumplen el alcance</h3>
            <v-list class="bg-transparent"
              v-if="resultado['Herramientas que no cumplen el alcance'] && resultado['Herramientas que no cumplen el alcance'].length">
              <v-list-item v-for="(herramienta, idx) in resultado['Herramientas que no cumplen el alcance']" :key="idx">
                <v-list-item-content>
                  <v-list-item-title>{{ herramienta.nombre }}</v-list-item-title>
                  <v-list-item-subtitle>
                    Faltan: <span style="color:#c62828;">{{ herramienta.modulos_faltantes.join(', ') }}</span>
                  </v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
            </v-list>
            <div v-else>
              Todas las herramientas cumplen el alcance.
            </div>
          </template>
          <div v-else-if="resultado && typeof resultado === 'string'">
            {{ resultado }}
          </div>
        </v-card-text>
      </v-card>
      <v-card class="my-5 pa-2 card-backgoundcustom" id="container" elevation="2"
        v-if="!resultado && !loading && !error">
        <v-card-item>
          <v-card-title class="text-h5"> Resultado del ranking </v-card-title>
        </v-card-item>
        <v-card-text class="pb-0">
          <div>Selecciona los módulos y presiona "Procesar"</div>
        </v-card-text>
      </v-card>

    </v-container>
  </LayoutDefault>
</template>
<style>
#container {
  width: 100%;
}

.card-backgoundcustom {
  background-color: #4d575839 !important;

}
</style>
<script setup>
import LayoutDefault from "@/components/Layout/Layoutmain.vue";
import { ref } from 'vue'
import axios from 'axios'

const selected = ref([])
const modulos = [
  "Gestión de Incidentes",
  "Gestión de Problemas",
  "Gestión de Cambios",
  "Gestión de Solicitudes de Servicio",
  "Catálogo de Servicios",
  "Gestión de Activos",
  "Gestión de Configuración (CMDB)",
  "Gestión de Niveles de Servicio (SLA)",
  "Gestión de Cambios Emergentes",
  "Gestión de Proyectos",
  "Gestión del Conocimiento",
  "Gestión de Despliegues (Release Management)",
  "Gestión de Contratos",
  "Gestión de Proveedores",
  "Observabilidad",
  "gaaaaa"
]

const resultado = ref(null)
const loading = ref(false)
const error = ref("")

async function consultarRanking() {
  if (selected.value.length === 0) {
    error.value = 'Selecciona al menos un módulo'
    return
  }
  error.value = ''
  resultado.value = null
  loading.value = true
  try {
    const res = await axios.post('http://localhost:3000/ranking-herramientas', {
      modulos: selected.value
    })
    resultado.value = res.data
    console.log('Resultado del ranking:', resultado.value)
  } catch (e) {
    error.value = 'Error consultando el backend'
    console.error('Error al consultar el ranking:', e)
  } finally {
    loading.value = false
  }
}
</script>
