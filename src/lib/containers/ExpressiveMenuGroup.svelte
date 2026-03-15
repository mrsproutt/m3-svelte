<script lang="ts">
  import type { Snippet } from "svelte";

  let {
    children,
  }: {
    children: Snippet;
  } = $props();
</script>

<div class="menu-group">
  {@render children()}
</div>

<style>
  .menu-group {
    display: flex;
    flex-direction: column;
    gap: 2px;
  }

  .menu-group > :global(.m3-container) {
    border-radius: var(--m3-shape-small);
  }

  .menu-group > :global(.m3-container):first-child {
    border-top-left-radius: var(--m3-shape-large);
    border-top-right-radius: var(--m3-shape-large);
  }

  .menu-group > :global(.m3-container):last-child {
    border-bottom-left-radius: var(--m3-shape-large);
    border-bottom-right-radius: var(--m3-shape-large);
  }
</style>
