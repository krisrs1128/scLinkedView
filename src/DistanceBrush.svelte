<script>
import distances from "./distances"
import * as d3 from 'd3';
export let highlight;

let brushElement;
let x_range;
let width, height = 30;

function update_selection(ev) {
  x_range = ev.selection;
  highlight = []
  for (let i = 0; i < bins.length; i++) {
    if (x_scale(bins[i].x0) > x_range[0] && x_scale(bins[i].x1) < x_range[1]) {
      for (let j = 0; j < bins[i].length; j++ ){
        highlight.push(bins[i][j].feature)
      }
    }
  }
}

let bins, x_scale, y_scale;

$: x_scale = d3.scaleLinear()
    .domain(d3.extent(distances.map(d => d.dist)))
    .range([0, 0.9 * width])
$: y_scale = d3.scaleLinear()
    .domain(d3.extent(bins.map(d => d.length)))
    .range([0, height])
$: bins = d3.histogram()
    .value(d => d.dist)
    .domain(x_scale.domain())
    .thresholds(80)(distances)
$: brush = d3.brushX()
    .extent([[0, 0 * height], [width, height]])
    .on("brush end", update_selection)
$: if (brushElement && width != null) {
  d3.select(brushElement).call(brush)
}
</script>

<div class="relative flex flex-col min-w-0 break-words bg-white w-full mb-6 shadow-lg rounded">
  <div class="p-4 flex-auto">
    <div class="relative h-30-px" bind:clientWidth={width} bind:clientHeight={height}>
      <svg width={width} height={height}>
        {#if bins.length > 1}
          {#each bins as d}
            <rect 
              x={x_scale(d.x0)} 
              y = {height - y_scale(d.length)} 
              width={d3.max([10, x_scale(d.x1) -  x_scale(d.x0)])} 
              height={y_scale(d.length)} 
              fill="#7b7b7b"
            />
          {/each}
        {/if}
        <g bind:this={brushElement} width={width} height={height} />
      </svg>
    </div>
  </div>
</div>
