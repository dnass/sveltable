<script>
  import { setContext, getContext } from 'svelte';
  import { TABLE_KEY } from './Table.svelte';

  const { dataLength, perPage, currentPage } = getContext(TABLE_KEY);

  export let pageSize = null,
    state = {};

  $: perPage.set(pageSize);

  $: totalPages = Math.ceil($dataLength / $perPage);

  export const setPage = page =>
    currentPage.set(Math.max(Math.min(page, totalPages), 0));

  $: state = {
    currentPage: $currentPage,
    totalRows: $dataLength,
    firstVisibleRow: $perPage * $currentPage + 1,
    lastVisibleRow: Math.min($perPage * ($currentPage + 1), $dataLength),
    totalPages,
    isFirstPage: $currentPage === 0,
    isLastPage: $currentPage === totalPages - 1
  };
</script>

<slot />
