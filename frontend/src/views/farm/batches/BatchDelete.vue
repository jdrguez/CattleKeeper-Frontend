<template>
  <div class="container py-5 d-flex justify-content-center">
    <div class="card shadow-lg p-4 rounded-4 w-100" style="max-width: 600px; background-color: #fefefe;">
      <h2 class="text-danger-emphasis fw-semibold text-center mb-4">Delete Batch</h2>

      <div v-if="batch">
        <p class="fs-5 text-center">
          Are you sure you want to delete the batch
          <span class="fw-bold text-danger">"{{ batch.name }}"</span>?
        </p>
      </div>
      <div v-else class="text-muted text-center mb-3">
        Loading batch data...
      </div>

      <div v-if="error" class="alert alert-danger text-center">
        {{ error }}
      </div>

      <div class="d-flex justify-content-center gap-3 mt-4">
        <button @click="deleteBatch" class="btn btn-outline-danger px-4 rounded-pill shadow-sm">
          Yes, delete
        </button>
        <button @click="cancel" class="btn btn-outline-secondary px-4 rounded-pill shadow-sm">
          Cancel
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import api from '@/api/axios'

const route = useRoute()
const router = useRouter()

const batchSlug = route.params.batch_slug
const batch = ref(null)
const error = ref('')

onMounted(async () => {
  try {
    const response = await api.get(`/api/farm/batch/${batchSlug}/`)
    batch.value = response.data
  } catch (err) {
    error.value = 'Error al cargar los datos del lote'
  }
})

const deleteBatch = async () => {
  try {
    await api.delete(`/api/farm/batch/${batchSlug}/delete/`)
    router.push({ name: 'batch-list', params: { batch_slug: batchSlug } })
    toast.success('The batch was successfully deleted.')
  } catch (err) {
    error.value = 'Error al eliminar el lote'
    toast.error('Error deleting batch.')
  }
}

const cancel = () => {
  router.go(-1)
}
</script>
