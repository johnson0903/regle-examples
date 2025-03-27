<template>
  <div class="h-screen bg-white dark:bg-[#191a19] text-gray-900 dark:text-gray-200 p-6">
    <form @submit.prevent="submit" class="max-w-2xl mx-auto">
      <div class="flex flex-col gap-4">
        <!-- First Name -->
        <div class="flex flex-col gap-2">
          <div class="flex gap-2">
            <label required>First Name</label>
            <span class="text-red-600" v-if="r$.$fields.firstName.$isRequired">*</span>
          </div>

          <input
            v-model="form.firstName"
            type="text"
            class="border border-gray-300 dark:border-gray-700 dark:bg-neutral-600 dark:text-gray-200 rounded p-2"
          />
          <FieldError :errors="r$.$errors.firstName" />
        </div>

        <!-- Last Name -->
        <div class="flex flex-col gap-2">
          <div class="flex gap-2">
            <label required>Last Name</label>
            <span class="text-red-600" v-if="r$.$fields.lastName.$isRequired">*</span>
          </div>

          <input
            v-model="form.lastName"
            type="text"
            class="border border-gray-300 dark:border-gray-700 dark:bg-neutral-600 dark:text-gray-200 rounded p-2"
          />
          <FieldError :errors="r$.$errors.lastName" />
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
import { applyIf, minLength, required } from '@regle/rules';
import { defineRegleConfig, inferRules } from '@regle/core';

const condition = ref(true);
const isFormValid = ref(false);

const form = ref({
  firstName: '',
  lastName: '',
});

const rules = computed(() => {
  return inferRules(form, {
    firstName: {
      required,
      minLength: applyIf(condition, minLength(10)),
    },
    lastName: {
      required,
      minLength: applyIf(condition, minLength(15)),
    },
  });
});

const { useRegle } = defineRegleConfig({
  shortcuts: {
    fields: {
      $isRequired: (field) => !!field.$rules.required?.$active,
    },
  },
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
