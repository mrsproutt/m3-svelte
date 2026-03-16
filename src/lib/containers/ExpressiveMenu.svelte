<script lang="ts">
  import { easeEmphasizedDecel } from "$lib/misc/easing";
  import type { Snippet } from "svelte";
  import { fade, slide } from "svelte/transition";

  let {
    children,
    vibrant = false,
    open = true,
    label,
  }: {
    children: Snippet;
    vibrant?: boolean;
    open?: boolean;
    label?: string;
  } = $props();
</script>

{#if open}
  <div
    class="m3-container expressive-menu"
    class:vibrant
    in:slide={{
      easing: easeEmphasizedDecel,
      axis: "y",
      duration: 200,
    }}
    out:fade={{
      duration: 100,
    }}
  >
    {#if label}
      <span class="label">{label}</span>
    {/if}

    <div>
      {@render children()}
    </div>
  </div>
{/if}

<style>
  .m3-container {
    --m3-menuitem-text: var(--m3c-on-surface);
    --m3-menuitem-icon: var(--m3c-on-surface-variant);
    --m3-menuitem-selected: var(--m3c-tertiary-container-subtle);
    --m3-menuitem-on-selected: var(--m3c-on-tertiary-container-subtle);

    display: flex;
    flex-direction: column;
    padding: 4px;
    border-radius: var(--m3-shape-large);
    background-color: var(--m3c-surface-container-low);
    z-index: 2;
    box-shadow: var(--m3-elevation-3);

    overflow: hidden auto;
    scrollbar-width: thin;
    scroll-behavior: smooth;
    scrollbar-color: var(--m3-menuitem-icon) transparent;
  }

  .m3-container > div {
    min-width: 156px;
    width: auto;
    display: flex;
    gap: 4px;
    flex-direction: column;
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

  .label {
    @apply --m3-label-large;
    color: var(--m3-menuitem-icon);
    font-weight: 500;
    padding: 8px 12px 8px;
  }
</style>
