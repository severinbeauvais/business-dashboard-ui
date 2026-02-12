<script setup lang="ts">
import { isAuthorized } from '@/utils/authorizations'
import { AuthorizedActionsE } from '@/enums/authorized-actions-e'

const emit = defineEmits<{(e:'close'): void}>()

const t = useNuxtApp().$i18n.t

defineProps({
  display: { type: Boolean, required: true }
})

const deleteErrorDialogOptions = computed(() => ({
  title: t('text.dialog.annualReportReminders.title'),
  text: '', // content slot is used instead
  buttons: [] as DialogButtonI[] // button slot is used instead
}) as DialogOptionsI)

const close = () => {
  emit('close')
}

const save = () => {
  const success = true

  console.log('save') // eslint-disable-line no-console

  if (success) {
    close()
  }
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
        <h2 class="font-normal">
          Email Reminders
        </h2>
        <p class="text-bcGovGray-700">
          A reminder to file your annual report before the due date
        </p>
      </div>
      <UDivider class="my-6" />
      <div>
        <h2 class="font-normal">
          Current Email Address
        </h2>
        <p class="text-bcGovGray-700">
          You will no longer receive annual report reminders. If you forget to file your annual
          report on time, your business will no longer be in good standing and may be dissolved.
        </p>
      </div>
      <template v-if="!isAuthorized(AuthorizedActionsE.NO_CONTACT_INFO)">
        <p>
          If you need help, please contact us.
        </p>
        <BcrosContactInfo :contacts="getContactInfo('registries')" class="mt-5" />
      </template>

      <!-- TODO: add error message in case of failure to save -->
    </template>

    <template #buttons>
      <div class="flex justify-center gap-5">
        <UButton
          variant="outline"
          class="px-10 py-2"
          @click="close()"
        >
          {{ $t('button.dialog.cancel') }}
        </UButton>
        <UButton
          class="px-10 py-2"
          data-cy="dissolution-button"
          @click="save()"
        >
          {{ $t('button.dialog.save') }}
        </UButton>
      </div>
    </template>
  </BcrosDialog>
</template>

<style scoped>
/*** TODO remove if not needed ***/
</style>
