<script setup lang="ts">
import { useQueryCache } from '@pinia/colada'

const queryCache = useQueryCache()
const prePopulated = ref(false)

function prePopulateCache() {
  // Simulates what happens when an optimistic mutation writes to a cache key
  // on a page where useInfiniteQuery() hasn't mounted yet.
  // e.g. user favorites a song from browse page, but the favorites
  // infinite query only mounts on the library page.
  queryCache.setQueryData(['items'], {
    pages: [['item-1', 'item-2', 'item-3']],
    pageParams: [1],
  })
  prePopulated.value = true
}
</script>

<template>
  <div>
    <h1>Home</h1>
    <p>
      <b>Step 1:</b> Click the button to pre-populate the cache with
      InfiniteData-shaped value via <code>setQueryData()</code>
      (before <code>useInfiniteQuery()</code> has been called for this key).
    </p>
    <button @click="prePopulateCache" :disabled="prePopulated">
      {{ prePopulated ? 'Cache pre-populated!' : 'Pre-populate cache' }}
    </button>
    <p v-if="prePopulated">
      <b>Step 2:</b>
      <NuxtLink to="/list">Navigate to List page</NuxtLink>
      — it will crash with
      <code>TypeError: Cannot set properties of undefined (setting 'value')</code>
    </p>
  </div>
</template>
