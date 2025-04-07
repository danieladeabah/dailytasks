<template>
  <NuxtPage />
  <UNotifications />
</template>

<script setup lang="ts">
import { useNotificationWatcher } from '@/composables/useNotificationWatcher'
import { meta } from '~~/meta'

useHead(meta)
useNotificationWatcher()
</script>
