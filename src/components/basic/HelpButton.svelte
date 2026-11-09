<script lang="ts">
  import type { Snippet } from "svelte";

  interface Props {
    /** Help text / markup shown when the button is pressed. */
    children: Snippet;
  }

  let { children }: Props = $props();

  let open = $state(false);
</script>

<span class="help-wrap">
  <button
    type="button"
    class="help-btn"
    class:active={open}
    aria-label="Help"
    aria-expanded={open}
    onclick={() => (open = !open)}>?</button>

  {#if open}
    <div class="help-pop">
      <button type="button" class="help-close" aria-label="Close" onclick={() => (open = false)}>×</button>
      <div class="help-content">{@render children()}</div>
    </div>
  {/if}
</span>

<style>
  .help-wrap {
    position: relative;
    display: inline-flex;
    vertical-align: middle;
  }
  .help-btn {
    width: 2em;
    height: 2em;
    min-width: 36px;
    min-height: 36px;
    border-radius: 50%;
    border: 1px solid currentColor;
    background: transparent;
    color: inherit;
    font-weight: 700;
    font-size: 1rem;
    line-height: 1;
    cursor: pointer;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 0;
    opacity: 0.85;
  }
  .help-btn:hover,
  .help-btn.active {
    opacity: 1;
    background: rgba(125, 125, 125, 0.25);
  }
  .help-pop {
    position: absolute;
    z-index: 1090;
    top: calc(100% + 6px);
    right: 0;
    left: auto;
    width: 320px;
    max-width: 88vw;
    background: var(--bs-body-bg, #212529);
    color: var(--bs-body-color, #dee2e6);
    border: 1px solid rgba(128, 128, 128, 0.4);
    border-radius: 8px;
    padding: 12px 14px;
    box-shadow: 0 6px 20px rgba(0, 0, 0, 0.45);
    font-size: 0.9rem;
    line-height: 1.45;
    text-align: left;
    font-weight: 400;
    white-space: normal;
    word-break: break-word;
    overflow-wrap: anywhere;
    max-height: 70vh;
    overflow-y: auto;
    overflow-x: hidden;
  }
  .help-close {
    position: absolute;
    top: 2px;
    right: 8px;
    background: none;
    border: none;
    color: inherit;
    font-size: 1.3rem;
    line-height: 1;
    cursor: pointer;
    opacity: 0.7;
  }
  .help-close:hover {
    opacity: 1;
  }
  .help-content {
    padding-right: 10px;
  }
</style>
