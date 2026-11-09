<!--
  Small looping diagram for the "batch print" (CSV) help popup.
  Shows: a list of texts  ->  one label template with a placeholder  ->
  several printed labels, one appearing per list row.
  Pure SVG + CSS, no dependencies, theme-neutral (uses currentColor + one accent).
-->
<script lang="ts"></script>
<div class="csv-anim" aria-hidden="true">
  <svg viewBox="0 0 300 104" xmlns="http://www.w3.org/2000/svg">
    <!-- ===== source list ===== -->
    <g class="grp">
      <!-- highlight behind each row (pulses in sequence) -->
      <rect class="hi h1" x="7" y="16" width="70" height="18" rx="4" />
      <rect class="hi h2" x="7" y="42" width="70" height="18" rx="4" />
      <rect class="hi h3" x="7" y="68" width="70" height="18" rx="4" />

      <rect class="row" x="7" y="16" width="70" height="18" rx="4" />
      <rect class="row" x="7" y="42" width="70" height="18" rx="4" />
      <rect class="row" x="7" y="68" width="70" height="18" rx="4" />

      <text class="num" x="16" y="29">1</text>
      <line class="ln" x1="26" y1="25" x2="70" y2="25" />
      <text class="num" x="16" y="55">2</text>
      <line class="ln" x1="26" y1="51" x2="70" y2="51" />
      <text class="num" x="16" y="81">3</text>
      <line class="ln" x1="26" y1="77" x2="70" y2="77" />
    </g>

    <!-- arrow 1 -->
    <path class="arr" d="M84 51 h14 m-5 -4 l5 4 l-5 4" />

    <!-- ===== template label with placeholder ===== -->
    <g class="grp">
      <rect class="tpl" x="108" y="34" width="72" height="36" rx="5" />
      <rect class="ph" x="116" y="44" width="56" height="16" rx="3" />
      <text class="phtext" x="144" y="56">&#123; &#125;</text>
    </g>

    <!-- arrow 2 -->
    <path class="arr" d="M188 51 h14 m-5 -4 l5 4 l-5 4" />

    <!-- ===== printed labels ===== -->
    <g class="grp">
      <g class="lbl l1">
        <rect class="tag" x="210" y="16" width="80" height="18" rx="4" />
        <text class="num" x="219" y="29">1</text>
        <line class="ln" x1="229" y1="25" x2="283" y2="25" />
      </g>
      <g class="lbl l2">
        <rect class="tag" x="210" y="42" width="80" height="18" rx="4" />
        <text class="num" x="219" y="55">2</text>
        <line class="ln" x1="229" y1="51" x2="283" y2="51" />
      </g>
      <g class="lbl l3">
        <rect class="tag" x="210" y="68" width="80" height="18" rx="4" />
        <text class="num" x="219" y="81">3</text>
        <line class="ln" x1="229" y1="77" x2="283" y2="77" />
      </g>
    </g>
  </svg>
</div>

<style>
  .csv-anim {
    width: 100%;
    margin-bottom: 10px;
  }
  .csv-anim svg {
    width: 100%;
    height: auto;
    display: block;
    --accent: #4dabf7;
  }
  .row,
  .tpl,
  .tag {
    fill: none;
    stroke: currentColor;
    stroke-width: 1.4;
    opacity: 0.85;
  }
  .ln {
    stroke: currentColor;
    stroke-width: 2;
    opacity: 0.55;
    stroke-linecap: round;
  }
  .num {
    fill: currentColor;
    font-size: 11px;
    font-weight: 700;
    opacity: 0.9;
  }
  .arr {
    fill: none;
    stroke: currentColor;
    stroke-width: 1.6;
    stroke-linecap: round;
    stroke-linejoin: round;
    opacity: 0.6;
  }
  .ph {
    fill: none;
    stroke: var(--accent);
    stroke-width: 1.4;
    stroke-dasharray: 4 3;
  }
  .phtext {
    fill: var(--accent);
    font-size: 12px;
    font-weight: 700;
    text-anchor: middle;
  }
  .tag {
    stroke: var(--accent);
  }

  /* printed labels appear one by one, then reset */
  .lbl {
    opacity: 0;
  }
  .l1 {
    animation: appear1 6s infinite ease;
  }
  .l2 {
    animation: appear2 6s infinite ease;
  }
  .l3 {
    animation: appear3 6s infinite ease;
  }
  @keyframes appear1 {
    0%, 8% { opacity: 0; }
    16%, 94% { opacity: 1; }
    100% { opacity: 0; }
  }
  @keyframes appear2 {
    0%, 36% { opacity: 0; }
    44%, 94% { opacity: 1; }
    100% { opacity: 0; }
  }
  @keyframes appear3 {
    0%, 64% { opacity: 0; }
    72%, 94% { opacity: 1; }
    100% { opacity: 0; }
  }

  /* source-row highlight pulses just before its label shows */
  .hi {
    fill: var(--accent);
    opacity: 0;
  }
  .h1 { animation: pulse1 6s infinite ease; }
  .h2 { animation: pulse2 6s infinite ease; }
  .h3 { animation: pulse3 6s infinite ease; }
  @keyframes pulse1 {
    0%, 6% { opacity: 0; }
    12% { opacity: 0.35; }
    22% { opacity: 0; }
    100% { opacity: 0; }
  }
  @keyframes pulse2 {
    0%, 34% { opacity: 0; }
    40% { opacity: 0.35; }
    50% { opacity: 0; }
    100% { opacity: 0; }
  }
  @keyframes pulse3 {
    0%, 62% { opacity: 0; }
    68% { opacity: 0.35; }
    78% { opacity: 0; }
    100% { opacity: 0; }
  }

  @media (prefers-reduced-motion: reduce) {
    .lbl { opacity: 1; }
    .lbl, .hi { animation: none; }
  }
</style>
