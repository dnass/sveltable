<script>
  import { setContext, getContext } from 'svelte';
  import { TABLE_KEY } from './Table.svelte';

  const { dataLength, perPage, currentPage: page } = getContext(TABLE_KEY);

  export let pageSize = null,
    totalRows,
    firstVisibleRow,
    lastVisibleRow,
    totalPages,
    currentPage = 0;

  export const setCurrentPage = p =>
    page.set(Math.max(Math.min(p, totalPages), 0));

  $: currentPage = $page;

  $: perPage.set(pageSize);

  $: totalPages = Math.ceil($dataLength / $perPage) - 1;

  $: totalRows = $dataLength;

  $: firstVisibleRow = $perPage * $page + 1;

  $: lastVisibleRow = Math.min($perPage * ($page + 1), $dataLength);
</script>

<slot />
