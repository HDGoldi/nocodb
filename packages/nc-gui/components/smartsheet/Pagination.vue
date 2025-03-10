<script setup lang="ts">
import { ChangePageInj, PaginationDataInj, computed, iconMap, inject, isRtlLang, useI18n } from '#imports'
import type { Language } from '~/lib'

const props = defineProps<{
  alignCountOnRight?: boolean
}>()

const { locale } = useI18n()

const paginatedData = inject(PaginationDataInj)!

const changePage = inject(ChangePageInj)!

const count = computed(() => paginatedData.value?.totalRows ?? Infinity)

const size = computed(() => paginatedData.value?.pageSize ?? 25)

const page = computed({
  get: () => paginatedData?.value?.page ?? 1,
  set: (p) => {
    changePage?.(p)
  },
})

const isRTLLanguage = computed(() => isRtlLang(locale.value as keyof typeof Language))
</script>

<template>
  <div class="flex items-center mb-1">
    <div class="flex-1">
      <span
        v-if="!alignCountOnRight && count !== null && count !== Infinity"
        class="caption ml-5 text-gray-500"
        data-testid="grid-pagination"
      >
        {{ count }} {{ count !== 1 ? $t('objects.records') : $t('objects.record') }}
      </span>
    </div>

    <a-pagination
      v-if="count !== Infinity"
      v-model:current="page"
      v-model:page-size="size"
      size="small"
      class="!text-xs !m-1 nc-pagination"
      :class="{ 'rtl-pagination': isRTLLanguage }"
      :total="count"
      show-less-items
      :show-size-changer="false"
    />
    <div v-else class="mx-auto flex items-center mt-n1" style="max-width: 250px">
      <span class="text-xs" style="white-space: nowrap"> Change page:</span>
      <a-input :value="page" size="small" class="ml-1 !text-xs" type="number" @keydown.enter="changePage(page)">
        <template #suffix>
          <component :is="iconMap.returnKey" class="mt-1" @click="changePage(page)" />
        </template>
      </a-input>
    </div>

    <div class="flex-1 text-right pr-2">
      <span
        v-if="alignCountOnRight && count !== null && count !== Infinity"
        class="caption mr-5 text-gray-500"
        data-testid="grid-pagination"
      >
        {{ count }} {{ count !== 1 ? $t('objects.records') : $t('objects.record') }}
      </span>
    </div>
  </div>
</template>

<style scoped>
:deep(.ant-pagination-item a) {
  @apply text-sm !leading-[21px] !no-underline;
}

:deep(.ant-pagination-item:not(.ant-pagination-item-active) a) {
  line-height: 21px !important;
  @apply text-sm !text-gray-500;
}

:deep(.ant-pagination-item-link) {
  @apply text-gray-500 flex items-center justify-center;
}

:deep(.rtl-pagination .ant-pagination-prev .ant-pagination-item-link),
:deep(.rtl-pagination .ant-pagination-next .ant-pagination-item-link) {
  @apply transform rotate-180;
}
</style>
