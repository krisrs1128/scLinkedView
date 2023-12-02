<!-- App.svelte -->
<script>
  import { Router, Route } from "svelte-routing";
  import Dashboard from "./Dashboard.svelte";
</script>
<Router>
<Route path="/" component="{Dashboard}" />
</Router>