<script lang="ts">
  import type { Snippet } from "svelte";
  import type { IconifyIcon } from "@iconify/types";

  import iconArrowRight from "@ktibow/iconset-material-symbols/arrow-right";
  import Icon from "$lib/misc/Icon.svelte";
  import { easeEmphasized } from "$lib/misc/easing";
  import { slide } from "svelte/transition";

  let {
    leadingIcon,
    trailingIcon,
    disabled = false,
    selected = false,
    onclick,
    label,
    details,
    submenuOpen = false,
    badge,
    onSubmenuClose,
    submenu,
    trailing,
  }: {
    leadingIcon?: IconifyIcon;
    trailingIcon?: IconifyIcon;
    disabled?: boolean;
    selected?: boolean;
    label: string;
    details?: string;
    submenuOpen?: boolean;
    badge?: Snippet;
    submenu?: Snippet;
    trailing?: Snippet;
    onSubmenuClose?: () => void;
    onclick?: (e: MouseEvent) => unknown;
  } = $props();

  const autoclose = (node: HTMLDetailsElement) => {
    const toggle = () => (submenuOpen = node.open);

    const close = (e: MouseEvent) => {
      if (e.target instanceof Element && node.contains(e.target)) return;

      node.open = false;
      submenuOpen = false;

      if (onSubmenuClose) onSubmenuClose();
    };

    window.addEventListener("click", close);
    node.addEventListener("toggle", toggle);

    return {
      destroy() {
        window.removeEventListener("click", close);
        node.removeEventListener("toggle", toggle);
      },
    };
  };
</script>

{#snippet item()}
  {#if leadingIcon}
    <span
      class="icon"
      transition:slide={{
        easing: easeEmphasized,
        axis: "x",
        duration: 200,
      }}
    >
      <Icon icon={leadingIcon} size={20} />
    </span>
  {/if}

  <div class="label">
    <span>{label}</span>

    {#if details}
      <span>{details}</span>
    {/if}
  </div>

  {#if badge}
    {@render badge()}
  {/if}

  {#if trailing && !submenu}
    <div class="trailing">
      {@render trailing()}
    </div>
  {/if}

  {#if trailingIcon || submenu}
    <span
      class="icon"
      transition:slide={{
        easing: easeEmphasized,
        axis: "x",
        duration: 200,
      }}
    >
      <Icon icon={submenu ? iconArrowRight : (trailingIcon as IconifyIcon)} size={20} />
    </span>
  {/if}
{/snippet}

{#if submenu}
  <!-- measure distance from page sides -->
  <details class="align-x} align-y}" open={submenuOpen} use:autoclose>
    <summary class="item m3-layer">
      {@render item()}
    </summary>

    {@render submenu()}
  </details>
{:else}
  <button type="button" class="item m3-layer" class:selected {disabled} {onclick}>
    {@render item()}
  </button>
{/if}

<style>
  .item {
    @apply --m3-label-large;
    @apply --m3-focus-inward;
    @apply --m3-label-large;
    border-radius: var(--m3-shape-extra-small);
    display: flex;
    align-items: center;
    justify-content: center;
    height: --m3-density(3rem);
    padding: 0 12px;
    height: 48px;
    gap: 8px;
    white-space: nowrap;

    border: none;

    background-color: transparent;
    color: var(--m3-menuitem-text);

    cursor: pointer;
    user-select: none;
    transition:
      border-radius var(--m3-easing-fast-spatial),
      background-color var(--m3-easing-fast),
      color var(--m3-easing-fast);
  }

  .trailing {
    color: var(--m3-menuitem-icon);
  }

  .item.selected {
    background-color: var(--m3-menuitem-selected);
    color: var(--m3-menuitem-on-selected);
    border-radius: var(--m3-shape-medium);
  }

  .item.selected .icon {
    color: var(--m3-menuitem-on-selected);
  }

  .item:first-of-type {
    border-top-left-radius: var(--m3-shape-medium);
    border-top-right-radius: var(--m3-shape-medium);
  }

  .item:last-of-type {
    border-bottom-left-radius: var(--m3-shape-medium);
    border-bottom-right-radius: var(--m3-shape-medium);
  }

  .label {
    flex: auto;
    display: flex;
    flex-direction: column;
    text-align: left;
  }

  .label > span:nth-child(2) {
    opacity: 0.5;
  }

  .icon {
    width: 20px;
    height: 20px;
  }
  .icon {
    color: var(--m3-menuitem-icon);
  }

  .item:disabled {
    color: --translucent(var(--m3-menuitem-text), 0.38);
    cursor: auto;
  }
  .item:disabled > .icon > :global(svg) {
    color: --translucent(var(--m3-menuitem-text), 0.38);
  }

  details {
    display: flex;
  }

  summary {
    flex: auto;
  }

  details > :global(:not(summary)) :global {
    position: absolute !important;
    z-index: 9999999;
    details.align-inner > & {
      left: 0;
    }
    details.align-right > & {
      right: 0;
    }
    details.align-down > & {
      top: 100%;
    }
    details.align-up > & {
      bottom: 100%;
    }
  }
</style>
