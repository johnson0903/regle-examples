<template>
  <div class="h-screen bg-white dark:bg-[#191a19] text-gray-900 dark:text-gray-200 p-6">
    <form @submit.prevent="submit" class="max-w-2xl mx-auto">
      <div class="flex flex-col gap-4">
        <!-- First Name -->
        <div class="flex flex-col gap-2">
          <label required>First Name</label>
          <input
            v-model="form.firstName"
            type="text"
            class="border border-gray-300 dark:border-gray-700 dark:bg-neutral-600 dark:text-gray-200 rounded p-2"
          />
          <FieldError :errors="r$.$errors.firstName" />
        </div>

        <!-- Last Name -->
        <div class="flex flex-col gap-2">
          <label required>Last Name</label>
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
          {{ isLoading ? 'Loading...' : 'Submit' }}
        </button>
      </div>

      <div v-if="isFormValid" class="mt-4 text-green-600"> Form is valid! </div>
    </form>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import { required, minLength } from '@regle/rules';
import FieldError from './components/FieldError.vue';
import { useRegle, type RegleComputedRules, type RegleExternalErrorTree } from '@regle/core';

const isFormValid = ref(false);
const isLoading = ref(false);

interface Form {
  firstName: string;
  lastName: string;
}

const form = ref<Form>({
  firstName: '',
  lastName: '',
});

const rules = computed(() => {
  return {
    firstName: {
      required,
      minLength: minLength(10),
    },
    lastName: {
      required,
      minLength: minLength(10),
    },
  } satisfies RegleComputedRules<typeof form>;
});

const externalErrors = ref<RegleExternalErrorTree<Form>>({});

const { r$ } = useRegle(form, rules, { externalErrors });

const submit = async () => {
  isFormValid.value = false;

  const { valid } = await r$.$validate();

  if (valid === false) return;

  isLoading.value = true;

  // Fake API request
  await new Promise((resolve) => setTimeout(resolve, 1000));

  // Fake backend validation errors
  externalErrors.value = {
    firstName: ['First name is already taken'],
    lastName: ['Last name is already taken'],
  };

  isLoading.value = false;
};
</script>
