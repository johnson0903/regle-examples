<template>
  <div class="h-screen bg-white dark:bg-[#191a19] text-gray-900 dark:text-gray-200 p-6">
    <form @submit.prevent="submit" class="max-w-2xl mx-auto">
      <div class="flex flex-col gap-4">
        <!-- Full Name -->
        <div class="flex flex-col gap-2">
          <label required>Full Name</label>
          <input
            v-model="form.fullName"
            type="text"
            class="border border-gray-300 dark:border-gray-700 dark:bg-neutral-600 dark:text-gray-200 rounded p-2"
          />
          <FieldError :errors="r$.$errors.fullName" />
        </div>
      </div>

      <!-- Children -->
      <h3 class="font-semibold my-4">Children</h3>

      <div v-for="(_, index) in form.children" class="border border-dashed border-gray-600 p-4 rounded-lg mt-4">
        <h3 class="font-semibold uppercase text-xs text-gray-400 mb-3"> Child {{ index + 1 }} </h3>

        <div class="grid grid-cols-2 gap-x-6 gap-y-4">
          <!-- Full Name -->
          <div class="flex flex-col gap-2">
            <label required>Full Name</label>
            <input
              v-model="form.children[index].fullName"
              type="text"
              class="border border-gray-300 dark:border-gray-700 dark:bg-neutral-600 dark:text-gray-200 rounded p-2"
            />
            <FieldError :errors="r$.$errors.children.$each[index].fullName" />
          </div>

          <!-- Age -->
          <div class="flex flex-col gap-2">
            <label required>Age</label>
            <input
              v-model="form.children[index].age"
              type="number"
              class="border border-gray-300 dark:border-gray-700 dark:bg-neutral-600 dark:text-gray-200 rounded p-2"
            />
            <FieldError :errors="r$.$errors.children.$each[index].age" />
          </div>
        </div>
      </div>

      <div class="flex items-center gap-4 mt-4">
        <button
          type="submit"
          class="bg-green-800 text-white rounded py-2 px-5 hover:bg-green-900 transition hover:active:bg-green-950"
        >
          Submit
        </button>
      </div>

      <div v-if="isFormValid" class="mt-4 text-green-600"> Form is valid! </div>
    </form>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import FieldError from './components/FieldError.vue';
import { minValue, minLength, required } from '@regle/rules';
import { inferRules, useRegle, type RegleComputedRules } from '@regle/core';

const isFormValid = ref(false);

const form = ref({
  fullName: '',
  children: [
    { fullName: '', age: 0 },
    { fullName: '', age: 0 },
  ],
});

const rules = computed(() => {
  return inferRules(form, {
    fullName: {
      required,
      minLength: minLength(10),
    },
    children: {
      $each: {
        fullName: {
          required,
          minLength: minLength(10),
        },
        age: {
          required,
          minValue: minValue(5),
        },
      },
    },
  });
});

const { r$ } = useRegle(form, rules);

const submit = async () => {
  isFormValid.value = false;

  const { valid } = await r$.$validate();

  if (valid) {
    isFormValid.value = true;
  }
};
</script>
