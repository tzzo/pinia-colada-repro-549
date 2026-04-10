<script setup lang="ts">
import { useInfiniteQuery } from '@pinia/colada'

const { data, hasNextPage } = useInfiniteQuery({
  key: ['items'],
  query: async ({ pageParam }) => {
    // Simulate an API call
    await new Promise((r) => setTimeout(r, 500))
    const start = (pageParam - 1) * 3
    return [`item-${start + 1}`, `item-${start + 2}`, `item-${start + 3}`]
  },
  initialPageParam: 1,
  getNextPageParam: (_lastPage, _allPages, lastPageParam) =>
    lastPageParam >= 3 ? null : lastPageParam + 1,
})
</script>

<template>
  <div>
    <h1>List</h1>
    <p v-if="data">
      Pages: {{ data.pages.length }}, hasNextPage: {{ hasNextPage }}
    </p>
    <ul>
      <li v-for="page in data?.pages" :key="page.toString()">
        {{ page }}
      </li>
    </ul>
  </div>
</template>
