<script lang="ts">
  import type { Snippet } from "svelte";

  let {
    children,
    anchored,
    open = true,
    x,
    y,
  }: {
    children: Snippet;
    anchored?: boolean;
    open?: boolean;
    x?: "start" | "end";
    y?: "down" | "up";
  } = $props();
</script>

{#if open}
  <div class="m3-container{x ? ' anchor-' + x : ''}{y ? ' anchor-' + y : ''}" class:anchored>
    {@render children()}
  </div>
{/if}

<style>
  @layer tokens {
    :root {
      --m3-menu-shape: var(--m3-shape-extra-small);
    }
  }
  .m3-container {
    display: flex;
    position: relative;
    flex-direction: column;
    padding: 0.5rem 0;
    border-radius: var(--m3-menu-shape);
    min-width: 7rem;
    max-width: 17.5rem;
    background-color: var(--m3c-surface-container);
    z-index: 2;
    box-shadow: var(--m3-elevation-2);
  }

  .m3-container.anchored {
    position: fixed;
    position-anchor: --m3-menu-anchor;
  }

  .m3-container.anchor-up {
    bottom: anchor(start);
  }

  .m3-container.anchor-down {
    top: anchor(end);
  }

  .m3-container.anchor-end {
    right: anchor(end);
  }

  .m3-container.anchor-start {
    left: anchor(start);
  }
</style>
