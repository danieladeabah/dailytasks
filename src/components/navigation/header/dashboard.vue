<template>
  <UiKitsUiSlotsHeaderSlot>
    <span>
      <NuxtLink to="/" class="text-2xl font-bold">{{ greeting }}</NuxtLink>
      <p>{{ currentDate }}</p>
    </span>
    <div class="flex gap-5">
      <UDropdown
        :items="items"
        :ui="{ item: { disabled: 'cursor-text select-text' } }"
        :popper="{ placement: 'bottom-start' }"
      >
        <UiKitsProfileImage
          v-if="isLoggedIn"
          :img-src="
            userInfo?.profile_image &&
            `https://raw.githubusercontent.com/danieladeabah/da-dailytasks/refs/heads/main/public/profiles/${userInfo?.profile_image}`
          "
          :name="userInfo?.first_name"
          :scale="true"
          :height-size="'2.5rem'"
          :width-size="'2.5rem'"
        />
        <UBadge v-else label="Get Started" color="gray" />

        <template v-if="isLoggedIn" #account="{ item }">
          <div class="flex items-center justify-between gap-5">
            <div class="text-left">
              <p class="truncate font-medium text-gray-900 dark:text-white">
                {{ item.label }}
              </p>
            </div>
          </div>
        </template>

        <template #item="{ item }">
          <span class="truncate">{{ item.label }}</span>
          <UIcon
            :name="item.icon"
            class="ms-auto h-4 w-4 flex-shrink-0 text-gray-400 dark:text-gray-500"
          />
        </template>
      </UDropdown>
    </div>

    <Analytics ref="analyticsModal" />
  </UiKitsUiSlotsHeaderSlot>
</template>

<script setup lang="ts">
import { useUser } from '@/composables/useUser'
import { useAuth } from '@/composables/useAuth'
import { useAuthenticationStore } from '@/store/auth'

const greeting = computed(() => {
  const hours = new Date().getHours()
  if (hours < 12) return 'Good morning'
  if (hours < 18) return 'Good afternoon'
  return 'Good evening'
})

const currentDate = new Date().toDateString()
const { userInfo } = useUser()
const { isLoggedIn } = useAuth()
const authStore = useAuthenticationStore()

const logout = () => {
  authStore.logout()
  navigateTo('/auth/login')
}

const analyticsModal = ref()

const items = computed(() => {
  const baseItems: Array<{
    label: string
    slot?: string
    disabled?: boolean
    icon?: string
    click?: () => void
  }> = [
    {
      label: isLoggedIn.value
        ? `${userInfo.value?.first_name ?? ''} ${userInfo.value?.last_name ?? ''}`
        : 'You are not logged in',
      slot: 'account',
      disabled: true
    }
  ]

  if (isLoggedIn.value) {
    baseItems.push(
      {
        label: 'Analytics',
        icon: 'i-heroicons-chart-pie-20-solid',
        click() {
          analyticsModal.value?.openModal()
        }
      },
      {
        label: 'Settings',
        icon: 'i-heroicons-cog-20-solid',
        click() {
          navigateTo('/settings')
        }
      }
    )
  }

  baseItems.push({
    label: isLoggedIn.value ? 'Logout' : 'Login',
    icon: isLoggedIn.value
      ? 'i-heroicons-arrow-right-start-on-rectangle'
      : 'i-heroicons-arrow-right-20-solid',
    click() {
      if (isLoggedIn.value) {
        logout()
      } else {
        navigateTo('/auth/login')
      }
    }
  })

  return [baseItems]
})
</script>
