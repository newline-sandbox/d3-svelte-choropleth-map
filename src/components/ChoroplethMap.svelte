<script lang="ts">
  import { extent, rollup } from "d3-array";
  import { csv, json } from "d3-fetch";
  import { geoPath } from "d3-geo";
  import { scaleLinear } from "d3-scale";
  import { onMount } from "svelte";
  import { type UsAtlas, mesh, feature } from "topojson";
  import Legend from "./Legend.svelte";

  export let datasets = [];
  export let colors = [];

  let dimensions = {
    width: 975,
    height: 720,
    margin: {
      top: 24,
      right: 0,
      left: 0,
      bottom: 6,
    },
  };

  const path = geoPath();

  let stateMesh = null;
  let statesFeatures = [];
  let scale = scaleLinear<string, string>()
    .domain([0, 1])
    .range(["#FFFFFF", "#FFFFFF"]);
  let ratios = new Map();
  let categories = datasets.map(({ label }) => label);

  onMount(async () => {
    const counts = await Promise.all(
      datasets.map(
        async ({ url }) =>
          await csv(url, ({ state, count }) => ({
            state,
            count: +count,
          }))
      )
    );

    const data = [...counts[0], ...counts[1]];

    const us: UsAtlas = await json("/data/us_topojson.json");

    stateMesh = mesh(us, us.objects.states, (a, b) => a !== b);

    statesFeatures = feature(us, us.objects.states).features;

    ratios = rollup(
      data,
      ([v1, v2]) => v1.count / v2.count,
      (d) => d.state
    );

    const [, max] = extent(Array.from(ratios.values()), (ratio) => ratio);

    scale = scaleLinear<string, string>()
      .domain([max, (max - 1) / 2, 1, 0.5, 0])
      .range(colors);
  });
</script>

<svg
  width={dimensions.width}
  height={dimensions.height}
  viewBox={`0 0 ${dimensions.width} ${dimensions.height}`}
>
  <g>
    <path
      fill="none"
      stroke="white"
      stroke-linejoin="round"
      d={path(stateMesh)}
    />
    {#each statesFeatures as feature}
      <path
        fill={scale(ratios.get(feature.properties.name))}
        d={path(feature)}
      />
    {/each}
  </g>
  <Legend {dimensions} {colors} {categories} />
</svg>
