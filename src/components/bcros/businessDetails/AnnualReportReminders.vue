<script setup lang="ts">
const { currentBusinessContact } = storeToRefs(useBcrosBusiness())
const emit = defineEmits<{(e:'close'): void}>()
const t = useNuxtApp().$i18n.t
const arReminder = ref(true) // the saved value
const isToggled = ref(true) // the toggle model value
const isSaving = ref(false) // true while saving
const isSaveError = ref(false) // true if there was a save error

defineProps({
  display: { type: Boolean, required: true }
})

const deleteErrorDialogOptions = computed(() => ({
  title: t('text.dialog.annualReportReminders.title'),
  text: '', // content slot is used instead
  buttons: [] as DialogButtonI[], // button slot is used instead
  headerLeft: true
}) as DialogOptionsI)

const close = () => {
  emit('close')
}

const save = async () => {
  // const success = true
  // if (success) {
  //   close()
  // }
  isSaving.value = true
  await new Promise(resolve => setTimeout(resolve, 1000))
  isSaveError.value = (Math.random() >= 0.5)
  arReminder.value = !arReminder.value
  isSaving.value = false
}
</script>

<template>
  <BcrosDialog
    attach="#todoList"
    name="deleteError"
    :display="display"
    :options="deleteErrorDialogOptions"
    @close="$emit('close')"
  >
    <template #content>
      <div>
        <!-- email reminders -->
        <h2 class="text-base">
          {{ $t('text.dialog.annualReportReminders.section1') }}
        </h2>

        <div class="flex items-center justify-between">
          <p class="text-bcGovGray-600">
            {{ $t('text.dialog.annualReportReminders.text1') }}
          </p>

          <label for="toggle" class="font-bold">
            {{ $t(!!arReminder
              ? 'text.dialog.annualReportReminders.emailsChecked'
              : 'text.dialog.annualReportReminders.emailsUnchecked'
            ) }}
          </label>

          <div class="w-10 h-5">
            <UIcon
              v-if="isSaving"
              class="text-2xl text-gray-700 animate-spin"
              name="i-mdi-loading"
            />
            <UToggle
              v-else
              id="toggle"
              v-model="isToggled"
              on-icon="i-heroicons-check-20-solid"
              off-icon="i-heroicons-x-mark-20-solid"
              color="blue"
              :ui="{
                inactive: 'bg-[#757575]',
                icon: { off: 'text-[#757575]' }
              }"
              @change="save()"
            />
          </div>
        </div>

        <p class="text-red-500 text-sm text-right h-6">
          {{ isSaveError ? $t('text.dialog.annualReportReminders.unableToSave') : '' }}
        </p>
      </div>

      <div v-if="arReminder">
        <UDivider class="mt-2" />

        <!-- current email address -->
        <div class="mt-7">
          <h2 class="text-base">
            {{ $t('text.dialog.annualReportReminders.section2') }}
          </h2>
          <p class="text-bcGovGray-600">
            {{ $t('text.dialog.annualReportReminders.text2') }}
          </p>
          <p class="tracking-wide">
            {{ currentBusinessContact.email }}
          </p>
        </div>
      </div>

      <!-- message box -->
      <div v-else>
        <BcrosMessageBox color="red" class="mt-6">
          <div class="flex items-start gap-3">
            <UIcon
              class="text-5xl text-yellow-500"
              name="i-mdi-warning"
            />
            <span>
              {{ $t('text.dialog.annualReportReminders.messageBoxText') }}
            </span>
          </div>
        </BcrosMessageBox>
      </div>
    </template>

    <template #buttons>
      <div class="flex justify-center mt-2">
        <UButton
          class="py-2 min-w-24 flex justify-center"
          :disabled="isSaving"
          @click="close()"
        >
          <UIcon
            v-if="isSaving"
            class="text-2xl text-white animate-spin"
            name="i-mdi-loading"
          />
          <div v-else class="text-base">
            {{ $t('button.dialog.done') }}
          </div>
        </UButton>
      </div>
    </template>
  </BcrosDialog>
</template>
