<script setup lang="ts">
const { form, setDefaultStrategy } = useFormSpaceSettings('setup');

const emit = defineEmits(['next']);

function nextStep() {
  emit('next');
  if (!form.value.strategies.length) return setDefaultStrategy();
}
</script>

<template>
  <div>
    <SettingsStrategiesBlock
      context="setup"
      :title="$t('setup.strategy.blockTitle')"
      hide-error
    />
    <div class="mx-4 md:mx-0">
      <SetupButtonNext
        :text="form.strategies.length ? 'next' : 'skip'"
        @click="nextStep"
      />
    </div>
  </div>
</template>
