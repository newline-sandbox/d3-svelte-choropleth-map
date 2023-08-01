<script>
  export let dimensions = {
    margin: {
      left: 0,
      right: 0,
      top: 0,
      bottom: 0,
    },
  };
  export let colors = [];
  export let categories = [];

  let labels = [categories[0], ...new Array(colors.length - 2), categories[1]];
</script>

<g
  transform={`translate(${dimensions.width - 120}, ${dimensions.height - 200})`}
>
  {#each colors as color, i}
    <g transform={`translate(0, ${i * 24})`}>
      <rect height="20" width="20" fill={color} />
      {#if labels[i]}
        <text
          dominant-baseline="middle"
          y="14"
          x="28"
          font-size="12"
          font-weight="bold"
        >
          {labels[i]}
        </text>
      {/if}
    </g>
  {/each}
</g>
