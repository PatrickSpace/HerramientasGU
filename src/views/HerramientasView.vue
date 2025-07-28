<template>
  <v-app>
    <v-main>
      <v-container class="py-10" style="max-width:700px;">
        <v-card>
          <v-card-title>
            <v-icon class="mr-2">mdi-cog-outline</v-icon>

            Ranking de Herramientas ITSM
          </v-card-title>
          <v-card-text>
            <p class="mb-4">Selecciona los módulos requeridos:</p>
            <v-row>
              <v-col cols="12" sm="6" v-for="mod in allModules" :key="mod">
                <v-checkbox v-model="modulosSeleccionados" :label="mod" :value="mod" hide-details color="primary" />
              </v-col>
            </v-row>
            <v-btn color="primary" @click="consultarRanking" :disabled="loading" class="my-4" block size="large">
              <v-icon left>mdi-magnify</v-icon>

              Consultar ranking
            </v-btn>

            <v-progress-linear v-if="loading" indeterminate color="primary" class="mb-4" />

            <v-alert v-if="error" type="error" class="mb-4" dense>

              {{ error }}
            </v-alert>

            <template v-if="resultado">
              <v-divider class="my-4"></v-divider>
              <h3 class="mb-2">
                <v-icon class="mr-1">mdi-check-circle-outline</v-icon>

                Herramientas que cumplen el alcance
              </h3>
              <v-alert v-if="resultado['Herramientas que cumplen el alcance'].length === 0" type="warning" dense
                class="mb-4">

                Ninguna herramienta cubre todos los módulos seleccionados.
              </v-alert>
              <v-list v-else>
                <v-list-item v-for="h in resultado['Herramientas que cumplen el alcance']" :key="h.nombre">
                  <v-list-item-content>
                    <v-list-item-title>
                      <strong>{{ h.nombre }}</strong>
                    </v-list-item-title>
                    <v-list-item-subtitle>

                      Precio: <strong>{{ h.precio }}</strong>,

                      Módulos que cubre: {{ h.modulos_cubre }}
                    </v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
              </v-list>

              <v-divider class="my-4"></v-divider>
              <h3 class="mb-2">
                <v-icon class="mr-1">mdi-cancel</v-icon>

                Herramientas que no cumplen el alcance
              </h3>
              <v-list>
                <v-list-item v-for="h in resultado['Herramientas que no cumplen el alcance']" :key="h.nombre">
                  <v-list-item-content>
                    <v-list-item-title>
                      <strong>{{ h.nombre }}</strong>
                    </v-list-item-title>
                    <v-list-item-subtitle>

                      Faltan: <span style="color:#c62828;">{{ h.modulos_faltantes.join(', ') }}</span>
                    </v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
              </v-list>
            </template>
          </v-card-text>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>

import { ref } from 'vue'

import axios from 'axios'

// Los mismos módulos que en el backend:

const allModules = [

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

  "Monitoreo",

  "Observabilidad"

]

const modulosSeleccionados = ref([])

const resultado = ref(null)

const loading = ref(false)

const error = ref('')

async function consultarRanking() {

  if (modulosSeleccionados.value.length === 0) {

    error.value = 'Selecciona al menos un módulo'

    return

  }

  error.value = ''

  resultado.value = null

  loading.value = true

  try {

    const res = await axios.post('http://localhost:3000/ranking-herramientas', {
      modulos: modulosSeleccionados.value
    })
    //console.log('Consultando ranking con módulos:', modulosSeleccionados.value)

    resultado.value = res.data
    console.log('Resultado del ranking:', resultado.value)

  } catch (e) {

    error.value = 'Error consultando el backend'

  } finally {

    loading.value = false

  }

}
</script>