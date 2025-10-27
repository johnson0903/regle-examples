<script setup lang="ts">
import { useRegle } from '@regle/core';
import { and, checked, dateBefore, email, minLength, required, withMessage } from '@regle/rules';
import FieldError from './components/FieldError.vue';
import { NDatePicker, NTimePicker } from 'naive-ui'
import { ref } from 'vue'
import { DateTime } from 'luxon'

interface Form {
  date: string,
  time: string
}

const targetDateTime = ref('2025-10-27 12:00:00')

function checkDate() {
  const groupedDate = DateTime.fromFormat(`${r$.$value.date} ${r$.$value.time}`, 'yyyy-MM-dd HH:mm:ss')
  const targetDate = DateTime.fromFormat(targetDateTime.value, 'yyyy-MM-dd HH:mm:ss')

  return groupedDate < targetDate
}

const { r$ } = useRegle({} as Form, {
  date: {
    required,
    checkDate: withMessage(
      checkDate,
      `date should before ${targetDateTime.value}`
    )
  },
  time: {
    required,
    checkDate: withMessage(
      checkDate,
      `date should before ${targetDateTime.value}`
    )
  }
}, {
  validationGroups: (fields) => ({
    myDateTime: [fields.date, fields.time]
  })
});

async function submit() {
  const { valid, data } = await r$.$validate();
  if (valid) {
    alert('Your form is valid!');
    console.log(data);
    //           ^ Hover type here to see type safe result
  }
}
</script>

<template>
  <div class="px-6 text-gray-900 antialiased">
    <div class="mx-auto max-w-xl divide-y py-12 md:max-w-4xl">
      <div class="py-12 flex flex-col justify-center items-center">
        <h2 class="text-2xl font-bold">Simple Regle</h2>
        <pre>{{ r$.$groups.myDateTime }}</pre>

        <div class="mt-8 w-96 max-w-md">
          <div class="grid grid-cols-1 gap-6">
            <div class="flex">
              <label class="block">
                <n-date-picker v-model:formatted-value="r$.$value.date" value-format="yyyy-MM-dd"
                  :status="r$.$groups.myDateTime.$error ? 'error' : undefined" type="date" clearable />
                <FieldError :errors="r$.date.$errors" />
              </label>
              <label class="block">
                <n-time-picker v-model:formatted-value="r$.$value.time" value-format="HH:mm:ss" clearable :status="r$.$groups.myDateTime.$error ? 'error' : undefined" />
              </label>
            </div>

            <h2>Target Date:</h2>
            <n-date-picker v-model:formatted-value="targetDateTime" value-format="yyyy-MM-dd HH:mm:ss"
              />

            <div class="flex justify-between">
              <button
                class="bg-white hover:bg-gray-100 text-gray-800 font-semibold py-2 px-4 border border-gray-400 rounded shadow"
                @click="r$.$reset({ toInitialState: true })">
                Reset
              </button>
              <button class="bg-indigo-500 text-white hover:bg-indigo-600 font-semibold py-2 px-4 rounded shadow"
                @click="submit">
                Submit
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
