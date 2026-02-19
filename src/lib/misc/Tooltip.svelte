<script lang="ts">
  import type { Snippet } from "svelte";

  let { children, text, side, position, rich, subhead, buttons } = $props<
    {
      children: Snippet;
      text: string;
      side?: "bottom" | "top" | "left" | "right";
      position?: "start" | "end" | "center";
    } & (
      | {
          rich: true;
          subhead?: string;
          buttons?: Snippet;
        }
      | {
          rich?: false;
        }
    )
  >();
  let activeTooltip: HTMLDivElement;
  let tooltipTarget: HTMLElement;
  let active = $state(false);

  const calculatePosition = (node: HTMLDivElement) => {
    const targetRect = tooltipTarget.getBoundingClientRect();
    const tooltipRect = node.getBoundingClientRect();

    let offsetTop = 0;
    let offsetLeft = 0;

    switch (side) {
      case "top":
        offsetTop = -tooltipRect.height - 4;

        break;

      case "bottom":
        offsetTop = targetRect.height + 4;

        break;

      case "left":
        offsetLeft = -tooltipRect.width - 4;

        break;

      case "right":
        offsetLeft = targetRect.width + 4;

        break;

      default:
        break;
    }

    if ((side === "top" || side === "bottom") && position === "center")
      offsetLeft += (targetRect.width - tooltipRect.width) / 2;
    if ((side === "left" || side === "right") && position === "center")
      offsetTop += (targetRect.height - tooltipRect.height) / 2;
    if ((side === "top" || side === "bottom") && position === "end")
      offsetLeft += targetRect.width - tooltipRect.width;
    if ((side === "left" || side === "right") && position === "end")
      offsetTop += targetRect.height - tooltipRect.height;

    return {
      top: targetRect.top + offsetTop,
      left: targetRect.left + offsetLeft,
    };
  };

  side ??= "top";
  position ??= "center";
</script>

<svelte:window
  onscroll={() => {
    if (active) {
      const { top, left } = calculatePosition(activeTooltip);

      activeTooltip.style.top = top + "px";
      activeTooltip.style.left = left + "px";
    }
  }}
/>

<!-- svelte-ignore a11y_no_static_element_interactions -->
<!-- svelte-ignore a11y_mouse_events_have_key_events -->
<div
  class="tooltip-container"
  {@attach (node) => {
    // should only have one child
    tooltipTarget = node.children[0] as HTMLElement;

    tooltipTarget.addEventListener("mouseover", () => (active = true));
    tooltipTarget.addEventListener("mouseout", () => (active = false));
  }}
>
  {@render children()}
</div>

{#if active}
  <div
    class="m3-container"
    class:rich
    {@attach (node) => {
      activeTooltip = node;

      const { top, left } = calculatePosition(node);

      node.style.top = top + "px";
      node.style.left = left + "px";
    }}
  >
    {text}
  </div>
{/if}

<style>
  .tooltip-container {
    display: contents;
  }

  .m3-container {
    position: fixed;
    z-index: 9;
    user-select: none;
  }

  .m3-container:not(.rich) {
    /* inverse-surface looks wrong */
    background: var(--m3c-on-surface);
    color: var(--m3c-surface);
    border-radius: var(--m3-shape-extra-small);
    @apply --m3-label-medium;
    padding: 4px 8px;
    width: fit-content;
    font-weight: normal;
    pointer-events: none;
    max-width: 200px;
  }

  .m3-container.rich {
    width: 312px;
  }
</style>
