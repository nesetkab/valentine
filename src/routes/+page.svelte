<script lang="ts">
  import Valentine from "./Valentine.svelte";
</script>

<svelte:head>
  <title>valentine</title>
  <meta name="description" content="valentine" />
</svelte:head>

<section>
  <Valentine />
</section>

<style>
  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
</style>
