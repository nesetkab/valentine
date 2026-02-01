<script lang="ts">
  export let title: string = "";

  let isHovered: boolean = false;
  let x: number = 0;
  let y: number = 0;

  function mouseOver(event: MouseEvent): void {
    isHovered = true;
    x = event.pageX + 5;
    y = event.pageY + 5;
  }

  function mouseMove(event: MouseEvent): void {
    x = event.pageX + 5;
    y = event.pageY + 5;
  }

  function mouseLeave(): void {
    isHovered = false;
  }
</script>

<div
  on:mouseover={mouseOver}
  on:mouseleave={mouseLeave}
  on:mousemove={mouseMove}
  role="tooltip"
>
  <slot />
</div>

{#if isHovered}
  <div style="top: {y}px; left: {x}px;" class="tooltip">{title}</div>
{/if}

<style>
  .tooltip {
    border: 1px solid #ddd;
    box-shadow: 1px 1px 1px #ddd;
    background: white;
    border-radius: 4px;
    padding: 4px;
    position: absolute;
  }
</style>
