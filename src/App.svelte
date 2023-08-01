<script lang="ts">
  import ChoroplethMap from "./components/ChoroplethMap.svelte";

  let datasets = [
    {
      label: "Dunkin' Donuts",
      url: "/data/dunkin_donuts_locations_counts.csv",
    },
    { label: "Starbucks", url: "/data/starbucks_locations_counts.csv" },
  ];

  let colors = ["#ea4f19", "#b15222", "#75542a", "#3c5733", "#005739"];
</script>

<main>
  <ChoroplethMap {datasets} {colors} />
</main>

<style>
  main {
    font-family: "Helvetica";
    display: flex;
    justify-content: center;
  }
</style>
