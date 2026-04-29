<script lang="ts">
  import type { Snippet } from "svelte";
  import type { IconifyIcon } from "@iconify/types";

  import iconArrowRight from "@ktibow/iconset-material-symbols/arrow-right";
  import { easeEmphasized } from "$lib/misc/easing";
  import { slide } from "svelte/transition";
  import Icon from "$lib/misc/Icon.svelte";

  let {
    leadingIcon,
    trailingIcon,
    disabled = false,
    selected = false,
    label,
    details,
    submenuOpen = $bindable(false),
    badge,
    submenu,
    trailing,
    onclick,
  }: {
    leadingIcon?: IconifyIcon;
    trailingIcon?: IconifyIcon;
    disabled?: boolean;
    selected?: boolean;
    label: string;
    details?: string;
    submenuOpen?: boolean;
    badge?: Snippet;
    submenu?: Snippet<[boolean]>;
    trailing?: Snippet;
    onclick?: (e: MouseEvent) => unknown;
  } = $props();

  const autoclose = (node: HTMLDivElement) => {
    const close = (e: MouseEvent) => {
      if (!node.contains(e.target as HTMLElement)) submenuOpen = false;
    };

    window.addEventListener("click", close);

    return {
      destroy() {
        window.removeEventListener("click", close);
      },
    };
  };
</script>

<div class="m3-container" use:autoclose>
  <button
    type="button"
    class="item m3-layer"
    class:selected
    {disabled}
    onclick={(e: MouseEvent) => {
      onclick?.(e);

      if (submenu) {
        submenuOpen = !submenuOpen;
        e.stopPropagation();
      }
    }}
  >
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
  </button>

  {#if submenu}
    <div class="menu">
      {@render submenu(submenuOpen)}
    </div>
  {/if}
</div>

<style>
  .m3-container {
    anchor-scope: --m3-menu-anchor;
    anchor-name: --m3-menu-anchor;
  }

  .item {
    @apply --m3-label-large;
    @apply --m3-focus-inward;
    border-radius: var(--m3-shape-extra-small);
    display: flex;
    align-items: center;
    justify-content: center;
    height: --m3-density(3rem);
    padding: 0 12px;
    gap: 8px;
    width: 100%;
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

  .m3-container:first-child > .item {
    border-top-left-radius: var(--m3-shape-medium);
    border-top-right-radius: var(--m3-shape-medium);
  }

  .m3-container:last-child > .item {
    border-bottom-left-radius: var(--m3-shape-medium);
    border-bottom-right-radius: var(--m3-shape-medium);
  }

  .label {
    flex: auto;
    display: flex;
    flex-direction: column;
    text-align: left;
  }

  .label > span:first-child:has(+ span:nth-child(2)) {
    margin-bottom: -1px;
  }

  .label > span:nth-child(2) {
    @apply --m3-label-medium;
    color: var(--m3-menuitem-icon);
    margin-top: -1px;
  }

  .item.selected .label > span:nth-child(2) {
    color: var(--m3-menuitem-on-selected);
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
</style>
