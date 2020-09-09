<script context="module">
  export const TABLE_KEY = {};
</script>

<script>
  import { setContext, getContext } from 'svelte';
  import { writable } from 'svelte/store';

  export let data = [],
    columns = [],
    rowID = undefined;

  let sortedData = [],
    sortedColumn,
    direction;

  const perPage = writable(null),
    currentPage = writable(0),
    dataLength = writable(data.length);

  export let rows = [],
    headers = [];

  const comparators = {
    ascending: (a, b) => (a < b ? -1 : a > b ? 1 : a >= b ? 0 : NaN),
    descending: (a, b) => (b < a ? -1 : b > a ? 1 : b >= a ? 0 : NaN)
  };

  const setColumn = column => {
    if (!sortedColumn || column.id !== sortedColumn.id) {
      sortedColumn = column;
      direction = sortedColumn.defaultSortDirection || 'descending';
    } else {
      direction = direction === 'ascending' ? 'descending' : 'ascending';
    }
  };

  const sortRows = () => {
    const sort = sortedColumn.sort || sortedColumn.content;

    sortedData = data
      .slice()
      .sort((a, b) => comparators[direction](sort(a), sort(b)));
  };

  setContext(TABLE_KEY, {
    dataLength,
    perPage,
    currentPage
  });

  setColumn(columns.find(d => d.default) || columns[0]);

  $: dataLength.set(data.length);
  $: data, columns, sortedColumn, direction, sortRows();
  $: data, currentPage.set(0);

  $: rows = sortedData
    .filter((_, i) =>
      $perPage
        ? i >= $currentPage * $perPage && i < ($currentPage + 1) * $perPage
        : true
    )
    .map((row, i) => ({
      id: rowID ? row[rowID] : i,
      cells: columns.map(({ id, content, cellClass }) => ({
        column: id,
        content: content(row),
        cellClass: cellClass || ''
      }))
    }));

  $: headers = columns.map(col => ({
    header: col.title,
    sort: col.sort ? () => setColumn(col) : null,
    sortDirection: col.id === sortedColumn.id ? direction : null
  }));
</script>

<slot />
