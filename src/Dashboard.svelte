<script>

// core components
import * as d3 from "d3";
import GeneUmap from "./GeneUmap.svelte";
import RibbonPlot from "./RibbonPlot.svelte";
import CardTable from "./CardTable.svelte";
import DistanceBrush from "./DistanceBrush.svelte";

let selection,
  highlight = [],
  fill_scale = d3.scaleOrdinal()
    .domain(["original", "simulated"])
    .range(["#BF1725", "#7C9EA6"])

</script>

<div>
  <div class="flex flex-wrap">
    <div class="w-9/12 xl:w-8/12 px-4 h-16">
      <DistanceBrush bind:highlight/>
    </div>
    <div class="flex flex-row w-full">
      <div class="w-full xl:w-8/12 mb-12 xl:mb-0 px-4">
        <GeneUmap bind:selection bind:fill_scale bind:highlight/>
      </div>
      <div class="w-full xl:w-4/12 mb-12 xl:mb-0 px-4">
        <RibbonPlot bind:selection bind:fill_scale/>
      </div>
    </div>
  </div>
  <div class="flex flex-wrap mt-4">
    <div class="w-full xl:w-8/12 px-4">
      <CardTable bind:highlight/>
    </div>
    <div class="w-full xl:w-4/12 px-4">
      <h2 class="text-blueGray-900 text-xl font-semibold" style="margin-bottom: 8px">
        <code>scDesigner</code> Model Criticism
      </h2>
        <p>
        You can use this interface to critique a fitted simulator. The UMAP
        panel (left) gives broad overview while the pseudotime series and table
        panels give gene-level details. These data describe gene expression
        activity in developing pancreas cells, as documented by 
        <a href="https://doi.org/10.1242/dev.173849">Bastidas-Ponce et al. (2019)</a> 
        and shared in the <a href="https://scvelo.readthedocs.io/en/stable/scvelo.datasets.pancreas/">scvelo package</a>.
        </p>
        <h3 class="text-blueGray-900 text-l font-semibold" style="margin-top: 8px; margin-bottom: 4px">Interpretation</h3>
        <p>
        The UMAP embeds summary statistics derived from the <text style="color:
        #BF1725;">real</text> and <text style="color: #7C9EA6;">simulated</text>
        genes.  Specifically, we represent each real and simulated gene by its
        10%, 20%, ..., 90% quantiles for each of 10 evenly spaced pseudotime
        bins. These 900-dimensional vectors are then embedded using UMAP. The
        links connect the two embeddings for the real and simulated versions of
        a gene -- longer links indicate poorer approximations. The distribution
        of these lengths is encoded by the histogram in the top left. 
        </p>
        <p style="margin-top: 8px;">
        The panel on the right gives the pseudotime series of gene expression
        levels for a single gene. The same color scheme is used to distinguish
        real from simulated expression. The curve and ribbon represent the
        fitted mean and 2-se bands fitted by the marginal
        <code>scDesigner</code> model.
        </p>
        <h3 class="text-blueGray-900 text-l font-semibold" style="margin-top: 8px; margin-bottom: 4px">Interaction</h3>
        The panels are synchronized with one another:
        <ul>
          <li>Hovering over a gene in the UMAP will update the ribbon display
          with the associated gene.</li>
          <li>Brushing the histogram will highlight the genes with distances
          within the selected range. This also updates the selection status of
          genes within the lower table.</li>
          <li>Checking a gene in the lower table will toggle that gene's
          highlight status in the UMAP.</li>
        </ul>
        <p>
          You can learn more about <code>scDesigner</code> at our <a
          href="https://krisrs1128.github.io/scDesigner">package webpage</a>.
          This package is still under active development, and we welcome all
          feedback -- please send a message to <a
          href="mailto:ksankaran@wisc.edu">ksankaran@wisc.edu</a>.
        </p>
    </div>
  </div>
</div>

<style>
li {
  list-style-type: circle;
  transform: "translate(12, 0)";
}

ul {
  display: block;
  list-style-type: disc;
  padding-inline-start: 40px;
}

a {
  color: #276890;
}
</style>