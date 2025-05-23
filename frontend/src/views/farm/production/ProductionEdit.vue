<template>
  <div class="form-container mx-auto p-5" style="max-width: 600px;">
    <h1 class="form-title mb-4">Edit Production</h1>
    <form @submit.prevent="updateProduction" class="form-grid">
      <div class="form-group">
        <label for="production_type">Production Type:</label>
        <select id="production_type" v-model="form.production_type">
          <option v-for="option in productionTypeOptions" :key="option.value" :value="option.value">
            {{ option.label }}
          </option>
        </select>
      </div>

      <div class="form-group">
        <label for="quantity">Quantity:</label>
        <input id="quantity" v-model.number="form.quantity" type="number" required placeholder="Quantity" />
      </div>

      <div class="form-group">
        <label for="unit">Unit:</label>
        <select id="unit" v-model="form.unit">
          <option v-for="option in unitOptions" :key="option.value" :value="option.value">
            {{ option.label }}
          </option>
        </select>
      </div>

      <div class="form-group">
        <label for="date">Date:</label>
        <input id="date" v-model="form.date" type="date" required />
      </div>

      <div class="form-group full-width">
        <label for="notes">Notes:</label>
        <textarea id="notes" v-model="form.notes" placeholder="Notes" rows="3"></textarea>
      </div>

      <button type="submit" class="btn-submit">Update</button>
    </form>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import api from '@/api/axios';
import { useToast } from 'vue-toastification';

const toast = useToast()
const route = useRoute();
const router = useRouter();
const batchSlug = route.params.batch_slug;
const productionPk = route.params.production_pk;

const form = ref({
  production_type: '',
  quantity: '',
  unit: '',
  date: '',
  notes: ''
});

const originalProductionType = ref('');
const originalUnit = ref('');

const productionTypeOptions = [
  { value: 'MEAT', label: 'Meat' },
  { value: 'MILK', label: 'Milk' },
  { value: 'EGG', label: 'Egg' },
  { value: 'WOOL', label: 'Wool' }
];

const unitOptions = [
  { value: 'L', label: 'Liters' },
  { value: 'KG', label: 'Kilograms' },
  { value: 'U', label: 'Units' }
];

onMounted(async () => {
  try {
    const res = await api.get(`api/farm/batch/${batchSlug}/production/`);
    const prod = res.data.find(p => p.id === parseInt(productionPk));
    if (prod) {
      const matchingProductionType = productionTypeOptions.find(
        opt => opt.value === prod.production_type || opt.label === prod.production_type
      );

      const matchingUnit = unitOptions.find(
        opt => opt.value === prod.unit || opt.label === prod.unit
      );

      form.value = {
        production_type: matchingProductionType ? matchingProductionType.value : prod.production_type,
        quantity: prod.quantity,
        unit: matchingUnit ? matchingUnit.value : prod.unit,
        date: prod.date,
        notes: prod.notes
      };

      originalProductionType.value = matchingProductionType ? matchingProductionType.value : prod.production_type;
      originalUnit.value = matchingUnit ? matchingUnit.value : prod.unit;
    }
  } catch (error) {
    console.error('Error cargando producción:', error);
  }
});


const updateProduction = async () => {
  try {
    if (!form.value.production_type) {
      form.value.production_type = originalProductionType.value;
    }
    if (!form.value.unit) {
      form.value.unit = originalUnit.value;
    }

    await api.post(`api/farm/batch/${batchSlug}/production/${productionPk}/update/`, form.value);
    toast.success('The production has been updated successfully.');
    router.back();
  } catch (error) {
    console.error('Error actualizando producción:', error);
    toast.error('The batch could not be updated.');
  }
};
</script>


<style scoped>
.form-container {
  background: #fff;
  border-radius: 14px;
  box-shadow: 0 8px 25px rgba(0,0,0,0.1);
  color: #2c3e50;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.form-title {
  font-weight: 700;
  font-size: 2rem;
  color: #34495e;
  border-bottom: 2px solid #e1e8f0;
  padding-bottom: 0.5rem;
  margin-bottom: 1.5rem;
}

.form-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.25rem 2rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  font-size: 1rem;
}

.form-group.full-width {
  grid-column: 1 / -1;
}

label {
  margin-bottom: 0.5rem;
  font-weight: 600;
  color: #555;
}

input[type="number"],
input[type="date"],
select,
textarea {
  border: 2px solid #d1d9e6;
  border-radius: 10px;
  padding: 0.55rem 0.9rem;
  font-size: 1rem;
  transition: border-color 0.25s ease;
  font-family: inherit;
  resize: vertical;
}

input[type="number"]:focus,
input[type="date"]:focus,
select:focus,
textarea:focus {
  border-color: #5c9ded;
  outline: none;
  box-shadow: 0 0 6px rgba(92, 157, 237, 0.4);
}

textarea {
  min-height: 75px;
}

.btn-submit {
  grid-column: 1 / -1;
  background: #5c9ded;
  color: white;
  font-weight: 700;
  font-size: 1.1rem;
  padding: 0.7rem 0;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  box-shadow: 0 6px 12px rgba(92, 157, 237, 0.5);
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
  margin-top: 0.5rem;
  user-select: none;
}

.btn-submit:hover,
.btn-submit:focus {
  background: #3f7ddd;
  box-shadow: 0 8px 16px rgba(63, 125, 221, 0.6);
  outline: none;
}
</style>
