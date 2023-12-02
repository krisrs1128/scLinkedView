<script>
  import { DataHandler, Datatable, Th, ThFilter } from '@vincjo/datatables'
  import distances from "./distances"

  export let highlight;
  const handler = new DataHandler(distances, { rowsPerPage: 5 } )
  const rows = handler.getRows()
  let selected_ = [];

$: {
  if (highlight) {
    selected_ = highlight
  }
}
</script>

<Datatable {handler}>
  <table>
    <thead>
      <tr>
        <Th {handler} orderBy={(row) => selected_.indexOf(row.feature) != -1}>Selection</Th>
        <Th {handler} orderBy="feature">Gene</Th>
        <Th {handler} orderBy="dist" >Distance</Th>
        <Th {handler} orderBy="feature">Thumbnail</Th>
      </tr>
      <tr>
        <th class="selection" />
        <ThFilter {handler} filterBy="feature"/>
        <ThFilter {handler} filterBy="dist"/>
        <ThFilter {handler} filterBy="feature"/>
      </tr>
    </thead>
    <tbody>
      {#each $rows as row}
        <tr>
          <td class="selection">
            <input
                type="checkbox"
                on:click={() => {
                  let ix = selected_.indexOf(row.feature)
                  if (ix == -1) {
                    selected_.push(row.feature)
                  } else {
                    selected_.splice(ix, 1)
                  }
                  highlight = selected_
                  handler.select(row.feature)
                }}
                checked={selected_.indexOf(row.feature) != -1}
            />
          </td>
          <td>{row.feature}</td>
          <td>{row.dist}</td>
          <td><img src="./assets/data/thumbnail_{row.feature}.png" alt="gene pseudotime series thumbnail" width=200/></td>
        </tr>
      {/each}
    </tbody>
  </table>
</Datatable>

<style>
  thead {
      background: #fff;
  }
  tbody td {
      border: 1px solid #f5f5f5;
      padding: 4px 20px;
  }
</style>