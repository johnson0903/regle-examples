<script setup lang="ts">
import { useRegle } from '@regle/core';
import { and, checked, dateBefore, email, minLength, required, withMessage } from '@regle/rules';
import FieldError from './components/FieldError.vue';
import { NDatePicker, NTimePicker } from 'naive-ui'
import { ref, watch } from 'vue'
import { DateTime } from 'luxon'
interface Form {
  date: string;
  time: string;
}

const { r$ } = useRegle({} as Form, {
  date: {
    required,
    checkBeforeDate: withMessage(
      () => {
        const date = DateTime.fromFormat(`${r$.$value.date} ${r$.$value.time}`, 'yyyy-MM-dd HH:mm:ss')
        const targetTime = DateTime.fromFormat(formattedValue.value, 'yyyy-MM-dd HH:mm:ss')

        console.log(date)
        console.log(targetTime)
        
        return date < targetTime
      },
      'date should before target time (trigger by date)'
    )
  },
  time: {
    required,
    checkBeforeDate: withMessage(
      () => {
        const date = DateTime.fromFormat(`${r$.$value.date} ${r$.$value.time}`, 'yyyy-MM-dd HH:mm:ss')
        const targetTime = DateTime.fromFormat(formattedValue.value, 'yyyy-MM-dd HH:mm:ss')
        return date < targetTime
      },
      'date should before target time (trigger by time)'
    )
  }
});

const formattedValue = ref('2025-10-27 12:00:00')

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
        <div class="mt-8 w-96 max-w-md">
          <div class="grid grid-cols-1 gap-6">
            <div class="flex justify-between">
              <label class="block">
                <span class="text-gray-700">Date</span>
                <n-date-picker v-model:formatted-value="r$.$value.date" value-format="yyyy-MM-dd" />
                <FieldError :errors="r$.date.$errors" />
              </label>
              <label class="block">
                <span class="text-gray-700">Time</span>
                <n-time-picker v-model:formatted-value="r$.$value.time" />
                <FieldError :errors="r$.time.$errors" />
              </label>
            </div>

            <div>
              <h3>Target Date</h3>
              <n-date-picker
              v-model:formatted-value="formattedValue"
              value-format="yyyy-MM-dd HH:mm:ss"
              type="datetime"
              clearable
              />
            </div>

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
