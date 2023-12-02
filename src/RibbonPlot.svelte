
<script>
import * as d3 from 'd3';
export let fill_scale;
export let selection;

let samples_ = {},
ribbon_ = {},
height, 
width,
load = () => {
  d3.json("https://raw.githubusercontent.com/krisrs1128/p5_examples/main/data/data.json").then(d => {
    samples_ = d
  })
  d3.json("https://raw.githubusercontent.com/krisrs1128/p5_examples/main/data/ribbon.json").then(d => {
    ribbon_ = d
  })
}
load()

$: samples = selection != undefined? samples_[selection] : []
$: ribbon = selection != undefined? ribbon_[selection] : []
$: x_scale = d3.scaleLinear()
    .domain([0, 1])
    .range([0.0 * width, 0.95 * width])
$: y_scale = d3.scaleLinear()
  .domain(d3.extent(samples.map(d => d.value)))
  .range([0.9 * height, 0.1 * height])
  .clamp(true)
$: ribbon_area = d3.area()
  .x(d => x_scale(d.pseudotime))
  .y0(d => y_scale(d.mu - 2 * d.sigma))
  .y1(d => y_scale(d.mu + 2 * d.sigma))
$: ribbon_line = d3.line()
  .x(d => x_scale(d.pseudotime))
  .y(d => y_scale(d.mu))

</script>
<div
  class="relative flex flex-col min-w-0 break-words w-full mb-6 shadow-lg rounded bg-blueGray-100"
>
  <div class="rounded-t mb-0 px-4 py-3 bg-transparent">
    <div class="flex flex-wrap items-center">
      <div class="relative w-full max-w-full flex-grow flex-1">
        <h6>
          Pseudotime Series
        </h6>
        <h2 class="text-blueGray-900 text-xl font-semibold">
          {#if selection}
            {selection}
          {:else}
          [none selected]
          {/if}
        </h2>
      </div>
    </div>
  </div>
  <div class="p-4 flex-auto" bind:clientHeight={height} bind:clientWidth={width}>
    <div class="relative h-350-px">
      <svg height={height} width={width}>
      {#if samples.length > 0}
        <path d={ribbon_area(ribbon)} fill={fill_scale("simulated")} fill-opacity=0.2 style="transition: all .5s ease;"/>
        <path d={ribbon_line(ribbon)} stroke={fill_scale("simulated")} fill="none" stroke-width=5 style="transition: all .5s ease;"/>
        {#each samples as d}
          <circle cx={x_scale(d.x)} fill={fill_scale(d.source)} cy={y_scale(d.value)} r=2 style="transition: all .5s ease;"/>
        {/each}
      {/if}
      </svg>
    </div>
  </div>
</div>
