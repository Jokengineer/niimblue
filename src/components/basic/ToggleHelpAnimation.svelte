<!--
  Small looping diagram for the help popup: shows the on/off switch being
  turned ON by a finger swipe, then dissolving and repeating.
  Pure SVG + CSS, no dependencies.
-->
<script lang="ts"></script>

<div class="tgl-anim" aria-hidden="true">
  <svg viewBox="0 0 150 54" xmlns="http://www.w3.org/2000/svg">
    <g class="scene">
      <!-- track: grey (off) with the accent (on) fading in over it -->
      <rect class="track-off" x="45" y="16" width="60" height="22" rx="11" />
      <rect class="track-on" x="45" y="16" width="60" height="22" rx="11" />

      <!-- sliding knob -->
      <circle class="knob" cx="56" cy="27" r="8" />

      <!-- finger / tap moving across -->
      <g class="finger">
        <circle cx="56" cy="40" r="7" />
        <circle class="ripple" cx="56" cy="27" r="11" />
      </g>
    </g>
  </svg>
</div>

<style>
  .tgl-anim {
    width: 100%;
    max-width: 150px;
    margin: 6px auto 2px;
  }
  .tgl-anim svg {
    width: 100%;
    height: auto;
    display: block;
    --accent: #4dabf7;
  }
  .track-off {
    fill: rgba(128, 128, 128, 0.35);
    stroke: currentColor;
    stroke-width: 1.2;
    opacity: 0.9;
  }
  .track-on {
    fill: var(--accent);
    stroke: none;
    opacity: 0;
    animation: trackOn 3.6s infinite ease;
  }
  .knob {
    fill: #f8f9fa;
    stroke: rgba(0, 0, 0, 0.25);
    stroke-width: 0.8;
    transform-box: fill-box;
    animation: knobMove 3.6s infinite ease;
  }
  .finger circle {
    fill: currentColor;
  }
  .finger circle:first-child {
    opacity: 0.75;
  }
  .ripple {
    fill: none;
    stroke: var(--accent);
    stroke-width: 2;
    opacity: 0;
    animation: ripple 3.6s infinite ease;
  }
  .finger {
    transform-box: fill-box;
    animation: fingerMove 3.6s infinite ease;
    opacity: 0;
  }
  .scene {
    animation: sceneFade 3.6s infinite ease;
  }

  @keyframes sceneFade {
    0% { opacity: 0; }
    6% { opacity: 1; }
    84% { opacity: 1; }
    94% { opacity: 0; }
    100% { opacity: 0; }
  }
  @keyframes knobMove {
    0%, 22% { transform: translateX(0); }
    42%, 100% { transform: translateX(36px); }
  }
  @keyframes trackOn {
    0%, 24% { opacity: 0; }
    42%, 100% { opacity: 1; }
  }
  @keyframes fingerMove {
    0% { opacity: 0; transform: translateX(0); }
    12% { opacity: 1; transform: translateX(0); }
    22% { transform: translateX(0); }
    42% { opacity: 1; transform: translateX(36px); }
    60% { opacity: 1; transform: translateX(36px); }
    74% { opacity: 0; transform: translateX(36px); }
    100% { opacity: 0; transform: translateX(36px); }
  }
  @keyframes ripple {
    0%, 36% { opacity: 0; transform: scale(0.5); }
    44% { opacity: 0.6; transform: scale(0.6); }
    60% { opacity: 0; transform: scale(1.1); }
    100% { opacity: 0; }
  }

  @media (prefers-reduced-motion: reduce) {
    .scene { opacity: 1; }
    .track-on { opacity: 1; }
    .knob { transform: translateX(36px); }
    .scene, .track-on, .knob, .finger, .ripple { animation: none; }
    .finger { opacity: 0; }
  }
</style>
