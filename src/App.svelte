<script>
  import Router from "svelte-spa-router";
  import routes from "./router";
  //export let name;
</script>

<body>
  <Router {routes} />
</body>

<style lang="postcss" global>
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
  @import url("https://fonts.googleapis.com/css2?family=Space+Mono:ital,wght@0,400;0,700;1,400;1,700&display=swap");
  html {
    font-family: "Space Mono", monospace;
  }
</style>
