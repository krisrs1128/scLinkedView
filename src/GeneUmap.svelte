<script>
  import * as d3 from "d3";
  export let selection;
  export let fill_scale;
  export let highlight;

  let embeddings = [],
    load = () => {
    d3.json("https://raw.githubusercontent.com/krisrs1128/p5_examples/main/data/umap.json").then(d => {
      embeddings = d
    })
  }

  function highlight_fun(d, highlight) {
    if (highlight && highlight.length > 0) {
      if (highlight.indexOf(d.feature) == -1) {
        return 0.1
      }
    }
    return 1;
  }

  function collapse(data) {
    let result = {}
    for (let i = 0; i < data.length; i++) {
      let ft = data[i].feature
      if (result[ft] === undefined) {
        result[ft] = [data[i]]
      } else {
        result[ft].push(data[i])
      }
    }
    return Object.values(result)
  }

  let width, height;
  load();

  function filter_collapsed(collapsed, highlight) {
    return collapsed.filter(d => {
      if (highlight && highlight.length > 0) {
        return highlight.indexOf(d[0].feature) != -1
      }
      return true;
    })
  }

  $: x_scale = d3.scaleLinear()
    .domain(d3.extent(embeddings.map(d => d.x)))
    .range([.1 * height, 0.9 * width])
  $: y_scale = d3.scaleLinear()
    .domain(d3.extent(embeddings.map(d => d.y)))
    .range([.1 * height, 0.9 * height])
  $: collapsed = collapse(embeddings)
  $: collapsed_subset = filter_collapsed(collapsed, highlight)
  $: neighborhoods = d3.Delaunay.from(
      collapsed_subset.map(
        d => [x_scale(0.5 * (d[0].x + d[1].x)), y_scale(0.5 * (d[0].y + d[1].y))]
      )
  )

  function update(ev) {
    let pos = d3.pointer(ev)
    let ix = neighborhoods.find(pos[0], pos[1])
    selection = collapsed_subset[ix][0].feature
  }
</script>

<div
  class="relative flex flex-col min-w-0 break-words w-full mb-6 shadow-lg rounded bg-blueGray-100"
>
  <div class="rounded-t mb-0 px-4 py-3 bg-transparent">
    <div class="flex flex-wrap items-center">
      <div class="relative w-full max-w-full flex-grow flex-1">
        <h6 class="uppercase text-blueGray-900 mb-1 text-xs font-semibold">
          Gene UMAP
        </h6>
        <h2 class="text-blueGray-900 text-xl font-semibold">
          Truth vs. Simulated
        </h2>
      </div>
    </div>
  </div>
  <div class="p-4 flex-auto">
    <div class="relative h-350-px" bind:clientWidth={width} bind:clientHeight={height}>
      <svg id="gene_umap" width={width} height={height} on:mousemove={update}>
        <g id="lines">
          {#each collapsed as d}
            <line 
              x1={x_scale(d[0].x)} 
              x2={x_scale(d[1].x)} 
              y1={y_scale(d[0].y)} 
              y2={y_scale(d[1].y)} 
              stroke="#d3d3d3" 
              opacity={highlight_fun(d[0], highlight)}
              stroke-width={selection == d[0].feature? 4 : 1}
            />
          {/each}
        </g>
        <g id="points">
          {#each embeddings as d}
            <circle 
              cx={x_scale(d.x)} 
              cy={y_scale(d.y)} 
              fill={fill_scale(d.source)} 
              opacity={highlight_fun(d, highlight)}
              r={selection == d.feature? 5 : 2}
            />
          {/each}
        </g>
      </svg>
    </div>
  </div>
</div>