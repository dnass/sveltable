<script>
  export let data = [],
    columns = [],
    pageSize = null,
    rowID = undefined;

  let sortedData = [],
    sortedColumn,
    direction,
    page = 0;

  export let rows = [],
    headers = [],
    pagination;

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

  setColumn(columns.find(d => d.default) || columns[0]);

  $: data, columns, sortedColumn, direction, sortRows();

  $: pages = Math.ceil(data.length / pageSize) - 1;

  $: setCurrentPage = p => (page = Math.max(Math.min(p, pages), 0));

  $: rows = sortedData
    .filter((_, i) =>
      pageSize ? i >= page * pageSize && i < (page + 1) * pageSize : true
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

  $: pagination = pageSize
    ? {
        totalRows: data.length,
        firstVisibleRow: pageSize * page + 1,
        lastVisibleRow: Math.min(pageSize * (page + 1), data.length),
        currentPage: page,
        setCurrentPage,
        goToPreviousPage: () => setCurrentPage(page - 1),
        goToNextPage: () => setCurrentPage(page + 1),
        isFirstPage: page === 0,
        isLastPage: page === pages
      }
    : null;
</script>

<slot />
