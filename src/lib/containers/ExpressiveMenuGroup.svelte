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

  /* important spam cause focus effect */
  .menu-group > :global(.m3-container.expressive-menu) {
    border-radius: var(--m3-shape-small) !important;
    width: auto;
  }

  .menu-group > :global(.m3-container.expressive-menu):first-child {
    border-top-left-radius: var(--m3-shape-large) !important;
    border-top-right-radius: var(--m3-shape-large) !important;
  }

  .menu-group > :global(.m3-container.expressive-menu):last-child {
    border-bottom-left-radius: var(--m3-shape-large) !important;
    border-bottom-right-radius: var(--m3-shape-large) !important;
  }
</style>
