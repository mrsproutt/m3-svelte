<script lang="ts">
  import { easeEmphasizedDecel } from "$lib/misc/easing";
  import type { Snippet } from "svelte";
  import { fade, slide } from "svelte/transition";

  let {
    children,
    vibrant = false,
    open = true,
    submenu = false,
    anchored = false,
    label,
    x,
    y,
  }: {
    children: Snippet;
    vibrant?: boolean;
    open?: boolean;
    label?: string;
    submenu?: boolean;
    anchored?: boolean;
    x?: "start" | "end";
    y?: "down" | "up";
  } = $props();

  let focused = $state<boolean>();

  // support for nested submenus
  const getParents = (node: HTMLDivElement) => {
    const parent = node.parentElement?.closest(".m3-container.expressive-menu") as HTMLDivElement;
    const parents: HTMLDivElement[] = [];

    if (parent?.contains(node)) {
      parents.push(parent);
      parents.push(...getParents(parent as HTMLDivElement));
    }

    return parents;
  };

  // hacky but it works
  const focusEffect = (node: HTMLDivElement) => {
    const parents = getParents(node);

    const mouseEnter = () => (focused = false);
    const mouseLeave = () => {
      focused = true;

      for (const parent of parents) {
        if (parent.matches(":hover"))
          parent?.dispatchEvent(
            new MouseEvent("mouseenter", {
              relatedTarget: node,
            }),
          );
      }
    };

    node.addEventListener("mouseleave", mouseLeave);
    node.addEventListener("mouseenter", mouseEnter);

    for (const parent of parents) parent?.addEventListener("mouseenter", mouseEnter);

    return {
      destroy: () => {
        node.removeEventListener("mouseleave", mouseLeave);
        node.removeEventListener("mouseenter", mouseEnter);

        for (const parent of parents) parent?.removeEventListener("mouseenter", mouseEnter);
      },
    };
  };
</script>

{#if open}
  <div
    class="m3-container expressive-menu {x ? 'anchor-' + x : ''} {y ? 'anchor-' + y : ''}"
    class:vibrant
    class:focused
    class:submenu
    class:anchored
    in:slide={{
      easing: easeEmphasizedDecel,
      axis: "y",
      duration: 200,
    }}
    out:fade={{
      duration: 100,
    }}
    use:focusEffect
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
    background-color: var(--m3c-surface-container-low);
    border-radius: var(--m3-shape-small);
    z-index: 2;
    box-shadow: var(--m3-elevation-3);

    overflow: hidden auto;
    scrollbar-width: thin;
    scroll-behavior: smooth;
    scrollbar-color: var(--m3-menuitem-icon) transparent;
    transition: border-radius var(--m3-easing-fast-spatial);
  }

  .m3-container.focused:not(:has(.m3-container.expressive-menu.focused)):is(:has(:global(.submenu)), .submenu),
  .m3-container:not(:has(:global(.m3-container.expressive-menu:hover))):is(:has(:global(.submenu)), .submenu):hover,
  :global(:not(.m3-container.expressive-menu)) .m3-container:not(.submenu) {
    border-radius: var(--m3-shape-large) !important;
  }

  .m3-container > div {
    min-width: 156px;
    width: auto;
    display: flex;
    gap: 4px;
    flex-direction: column;
  }

  .m3-container.anchored,
  .m3-container.submenu {
    position: fixed;
    position-anchor: --m3-menu-anchor;
  }

  .m3-container.submenu {
    left: anchor(end);
    top: anchor(start);
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
