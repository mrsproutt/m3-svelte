<script lang="ts">
  import type { Snippet } from "svelte";

  let {
    children,
    vibrant = false,
  }: {
    children: Snippet;
    vibrant?: boolean;
  } = $props();
</script>

<div class="m3-container" class:vibrant>
  {@render children()}
</div>

<style>
  .m3-container {
    --m3-menuitem-text: var(--m3c-on-surface);
    --m3-menuitem-icon: var(--m3c-on-surface-variant);
    --m3-menuitem-selected: var(--m3c-tertiary-container-subtle);
    --m3-menuitem-on-selected: var(--m3c-on-tertiary-container-subtle);

    min-width: 196px;
    display: flex;
    gap: 4px;
    flex-direction: column;
    padding: 4px;
    border-radius: var(--m3-shape-large);
    background-color: var(--m3c-surface-container-low);
    z-index: 2;
    box-shadow: var(--m3-elevation-2);

    overflow: auto;
    scrollbar-width: thin;
    scrollbar-color: transparent;
  }

  .m3-container.vibrant {
    --m3-menuitem-background: light-dark(
      var(--m3c-tertiary-container),
      var(--m3c-tertiary-container-subtle)
    );
    --m3-menuitem-text: light-dark(
      var(--m3c-on-tertiary-container),
      var(--m3c-on-tertiary-container-subtle)
    );
    --m3-menuitem-icon: var(--m3-menuitem-text);
    --m3-menuitem-selected: var(--m3-menuitem-text);
    --m3-menuitem-on-selected: var(--m3-menuitem-background);

    background-color: var(--m3c-tertiary-container-subtle);
  }
</style>
