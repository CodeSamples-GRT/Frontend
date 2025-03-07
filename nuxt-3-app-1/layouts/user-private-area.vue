﻿<template>
  <v-layout class="rounded rounded-md">
    <client-only>
      <user-private-area-layout-header />
      <user-private-area-layout-sidebar />
    </client-only>
    <v-main class="main-view">
      <slot />
    </v-main>
    <client-only>
      <DialogLayout />
    </client-only>
  </v-layout>
</template>

<script setup lang="ts">
import { useBaseFetch } from '~/composables/useBaseFetch'
import { useUserPrivateAreaStore } from '~/stores/user-private-area'
import { useUserPrivateAreaNotificationsStore } from '~/stores/user-private-area/notifications'
import { USER_PRIVATE_AREA_API_KEYS } from '~/constants/api-keys'

const { $emitGlobalEvent } = useNuxtApp()
const userPrivateAreaStore = useUserPrivateAreaStore()
const notificationsStore = useUserPrivateAreaNotificationsStore()

try {
  const response = await useBaseFetch('/user/me', {
    key: USER_PRIVATE_AREA_API_KEYS.USER_PRIVATE_AREA_ME
  })
  userPrivateAreaStore.setServerData((response.data.value as { data: {} })?.data || {})
} catch (err) {
  // TODO: add the proper error handling functionality once the back-end would be finalized
  console.log('🚀 → err:', err)
}

const onWindowResize = (event: Event) => $emitGlobalEvent('window:resize', event)

onMounted(() => {
  window.addEventListener('resize', onWindowResize)
  notificationsStore.enableSocket()
})

onUnmounted(() => {
  window.removeEventListener('resize', onWindowResize)
})
</script>

<style lang="scss" src="~/assets/scss/global/animations.scss"></style>
<style lang="scss">
// Notifications global styling
.Toastify__toast-container {
  width: auto;

  .Toastify__toast-body,
  .success-toast,
  .error-toast,
  .warning-toast,
  .notification-type-toast {
    padding: 0;
  }

  .success-toast,
  .error-toast,
  .warning-toast,
  .notification-type-toast {
    background-color: transparent;
    box-shadow: none;

    @include mobile {
      max-width: calc(100vw - 32px);
    }
  }

  .notification-type-toast {
    border-radius: 16px;
    backdrop-filter: blur(12px);
    box-shadow: 0px 28px 40px 0px rgba(0, 0, 0, 0.1);

    @include mobile {
      margin-bottom: 16px;
    }
  }
}
</style>
<style lang="scss" scoped>
.main-view {
  --padding-top: 20px;
  --padding-bottom: 24px;
  // Reflects the available height of the viewport
  --available-view-height: calc(
    100vh - var(--v-layout-top) - var(--padding-top) - var(--padding-bottom)
  );

  display: flex;
  margin-left: auto;
  width: 100%;
  background-color: $neutral-black-25;
  // The CSS-variable is being generated by Vuetify
  margin-top: var(--v-layout-top);
  min-height: calc(100vh - var(--v-layout-top));

  @include layoutSidebarNotCollapsible {
    padding: 20px 48px 24px;
    // The CSS-variable is being generated by Vuetify
    max-width: calc(100% - var(--v-layout-left));
  }

  @include layoutSidebarCollapsible {
    --padding-top: 0px;
    --padding-bottom: 0px;

    padding: 0;
  }

  & > * {
    width: 100%;
  }
}
</style>
