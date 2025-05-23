<template>
  <div class="container py-5">
    <h1 class="fw-bold text-primary-emphasis mb-4 border-bottom pb-2">
      Batch Details
    </h1>

    <div v-if="error" class="alert alert-danger rounded-3 shadow-sm">
      {{ error }}
    </div>

    <div v-else-if="batch" class="card shadow-lg border-0 rounded-4" style="background-color: #f9f9fb;">
  <div class="card-body p-4">
    <div class="row">
      <div class="col-md-6 mb-3">
        <p class="mb-2 d-flex align-items-center">
          <i class="bi bi-hash text-secondary me-2"></i>
          <strong class="me-1">Name:</strong> {{ batch.name }}
        </p>
        <p class="mb-2 d-flex align-items-center">
          <i class="bi bi-patch-question text-secondary me-2"></i>
          <strong class="me-1">Species:</strong> {{ batch.species }}
        </p>
        <p class="mb-2 d-flex align-items-center">
          <i class="bi bi-gender-ambiguous text-secondary me-2"></i>
          <strong class="me-1">Sex:</strong> {{ batch.sex }}
        </p>
        <p class="mb-2 d-flex align-items-center">
          <i class="bi bi-123 text-secondary me-2"></i>
          <strong class="me-1">Quantity:</strong> {{ batch.quantity }}
        </p>
      </div>

      <div class="col-md-6 mb-3">
        <p class="mb-2 d-flex align-items-center">
          <i class="bi bi-globe2 text-secondary me-2"></i>
          <strong class="me-1">Origin:</strong> {{ batch.origin }}
        </p>
        <p class="mb-2 d-flex align-items-center">
          <i class="bi bi-journal-text text-secondary me-2"></i>
          <strong class="me-1">Notes:</strong> {{ batch.notes || 'N/A' }}
        </p>
        <p class="mb-2 d-flex align-items-center">
          <i class="bi bi-calendar3 text-secondary me-2"></i>
          <strong class="me-1">Purchase date:</strong> {{ batch.purchase_date }}
        </p>
      </div>
    </div>

        <div class="d-flex flex-wrap gap-3 mt-4">
        <button class="btn btn-outline-primary rounded-pill shadow-sm d-flex align-items-center gap-2" @click="goToUpdate">
          <i class="bi bi-pencil align-middle"></i>
          <span class="align-middle">Update</span>
        </button>

        <button
          class="btn btn-outline-secondary rounded-pill shadow-sm d-flex align-items-center gap-2"
          @click="$router.push({ name: 'BatchAnimalList', params: { batch_slug: batch.slug } })"
        >
          <i class="bi bi-kanban align-middle"></i>
          <span class="align-middle">View animals</span>
        </button>

        <router-link
          :to="`/batch/${batch.slug}/delete`"
          class="btn btn-outline-danger rounded-pill shadow-sm d-flex align-items-center gap-2"
        >
          <i class="bi bi-trash align-middle"></i>
          <span class="align-middle">Delete</span>
        </router-link>

        <button
          class="btn btn-outline-success rounded-pill shadow-sm d-flex align-items-center gap-2"
          @click="goToProductions(batch.slug)"
        >
          <i class="bi bi-box-seam align-middle"></i>
          <span class="align-middle">View productions</span>
        </button>
      </div>

      </div>
    </div>

    <div v-else class="text-muted">
      <p>⏳ Loading batch...</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import api from '@/api/axios';

const route = useRoute();
const batch = ref(null);
const error = ref(null);
const router = useRouter();

onMounted(async () => {
  const slug = route.params.batch_slug;
  try {
    const response = await api.get(`api/farm/batch/${slug}/`);
    batch.value = response.data;
  } catch (err) {
    error.value = 'No se pudo cargar el lote.';
    console.error(err);
  }
});

const goToUpdate = () => {
  const batchSlug = route.params.batch_slug;
  router.push({ name: 'BatchUpdate', params: { batch_slug: batchSlug } });
};

const goToProductions = (slug) => {
  router.push({ name: 'ProductionList', params: { batch_slug: slug } });
};
</script>
