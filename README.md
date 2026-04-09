<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>The Wandering Verse</title>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=Libre+Baskerville:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet"/>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg:        #0B0F1A;
    --bg2:       #1A1F36;
    --ivory:     #F5F3EB;
    --gold:      #D9B382;
    --gold-dim:  rgba(217,179,130,.18);
    --grey:      #8A8A8A;
    --serif:     'Cormorant Garamond', Georgia, serif;
    --body:      'Libre Baskerville', Georgia, serif;
  }

  html { scroll-behavior: smooth; }

  body {
    background: var(--bg);
    color: var(--ivory);
    font-family: var(--body);
    font-size: 16px;
    line-height: 1.8;
    overflow-x: hidden;
    cursor: default;
  }

  /* ── SCROLLBAR ── */
  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: var(--bg); }
  ::-webkit-scrollbar-thumb { background: var(--gold-dim); border-radius: 2px; }

  /* ── SELECTION ── */
  ::selection { background: var(--gold-dim); color: var(--gold); }

  /* ── NAV ── */
  nav {
    position: fixed; top: 0; left: 0; right: 0; z-index: 100;
    padding: 1.6rem 3rem;
    display: flex; justify-content: space-between; align-items: center;
    background: linear-gradient(to bottom, rgba(11,15,26,.92) 0%, transparent 100%);
    backdrop-filter: blur(2px);
  }
  .nav-brand {
    font-family: var(--serif);
    font-size: 1.05rem;
    font-weight: 300;
    letter-spacing: .22em;
    text-transform: uppercase;
    color: var(--gold);
    opacity: .85;
    text-decoration: none;
    transition: opacity .4s;
  }
  .nav-brand:hover { opacity: 1; }
  .nav-links { display: flex; gap: 2.2rem; list-style: none; }
  .nav-links a {
    font-family: var(--serif);
    font-size: .88rem;
    letter-spacing: .14em;
    text-transform: uppercase;
    color: var(--grey);
    text-decoration: none;
    transition: color .4s;
    position: relative;
  }
  .nav-links a::after {
    content: '';
    position: absolute; bottom: -3px; left: 0; right: 0; height: 1px;
    background: var(--gold);
    transform: scaleX(0);
    transition: transform .4s cubic-bezier(.25,.46,.45,.94);
    transform-origin: right;
  }
  .nav-links a:hover { color: var(--gold); }
  .nav-links a:hover::after { transform: scaleX(1); transform-origin: left; }

  /* ── SOUND TOGGLE ── */
  .sound-btn {
    background: none; border: 1px solid rgba(217,179,130,.3);
    border-radius: 50%; width: 36px; height: 36px;
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; color: var(--grey); font-size: .9rem;
    transition: border-color .4s, color .4s;
  }
  .sound-btn:hover { border-color: var(--gold); color: var(--gold); }
  .sound-btn.active { border-color: var(--gold); color: var(--gold); }

  /* ── HERO ── */
  #hero {
    min-height: 100vh;
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    text-align: center;
    padding: 6rem 2rem;
    position: relative;
    overflow: hidden;
  }

  /* canvas sky */
  #sky-canvas {
    position: absolute; inset: 0;
    width: 100%; height: 100%;
    pointer-events: none;
  }

  /* gradient veil over canvas */
  .hero-veil {
    position: absolute; inset: 0;
    background: radial-gradient(ellipse at 50% 60%, transparent 20%, rgba(11,15,26,.6) 70%, var(--bg) 100%);
    pointer-events: none;
  }

  .hero-content {
    position: relative; z-index: 2;
    display: flex; flex-direction: column; align-items: center; gap: 1.4rem;
  }

  .hero-eyebrow {
    font-family: var(--serif);
    font-size: .82rem;
    letter-spacing: .35em;
    text-transform: uppercase;
    color: var(--gold);
    opacity: 0;
    animation: fadeUp .9s ease forwards .4s;
  }

  .hero-title {
    font-family: var(--serif);
    font-size: clamp(3.2rem, 8vw, 7rem);
    font-weight: 300;
    line-height: 1.05;
    letter-spacing: .04em;
    color: var(--ivory);
    opacity: 0;
    animation: fadeUp 1.1s ease forwards .7s;
  }

  .hero-title em {
    font-style: italic;
    color: var(--gold);
    font-weight: 300;
  }

  .hero-rule {
    width: 60px; height: 1px;
    background: linear-gradient(to right, transparent, var(--gold), transparent);
    opacity: 0;
    animation: fadeUp .8s ease forwards 1s;
  }

  .hero-sub {
    font-family: var(--serif);
    font-size: clamp(1rem, 2.5vw, 1.25rem);
    font-style: italic;
    color: var(--grey);
    max-width: 460px;
    font-weight: 300;
    opacity: 0;
    animation: fadeUp .9s ease forwards 1.2s;
  }

  .hero-scroll {
    position: absolute; bottom: 2.4rem; left: 50%; transform: translateX(-50%);
    z-index: 2; display: flex; flex-direction: column; align-items: center; gap: .5rem;
    opacity: 0;
    animation: fadeUp .8s ease forwards 2s;
  }
  .hero-scroll span {
    font-family: var(--serif);
    font-size: .72rem; letter-spacing: .22em;
    text-transform: uppercase; color: var(--grey);
  }
  .scroll-line {
    width: 1px; height: 40px;
    background: linear-gradient(to bottom, var(--gold), transparent);
    animation: scrollPulse 2.2s ease-in-out infinite;
  }

  @keyframes scrollPulse {
    0%, 100% { opacity: .4; transform: scaleY(1); }
    50% { opacity: 1; transform: scaleY(1.15); }
  }

  /* ── FADE IN KEYFRAMES ── */
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(22px); }
    to   { opacity: 1; transform: translateY(0); }
  }
  @keyframes fadeIn {
    from { opacity: 0; }
    to   { opacity: 1; }
  }

  /* ── SECTION BASE ── */
  section { padding: 7rem 2rem; }

  .container { max-width: 860px; margin: 0 auto; }
  .container-wide { max-width: 1100px; margin: 0 auto; }

  /* ── SECTION LABEL ── */
  .s-label {
    font-family: var(--serif);
    font-size: .78rem;
    letter-spacing: .32em;
    text-transform: uppercase;
    color: var(--gold);
    margin-bottom: 1rem;
    display: block;
  }

  /* ── ABOUT ── */
  #about {
    background: linear-gradient(to bottom, var(--bg) 0%, var(--bg2) 50%, var(--bg) 100%);
    position: relative;
  }
  #about::before {
    content: '';
    position: absolute; top: 0; left: 50%; transform: translateX(-50%);
    width: 1px; height: 80px;
    background: linear-gradient(to bottom, transparent, var(--gold-dim));
  }

  .about-inner {
    display: grid;
    grid-template-columns: 1fr 2fr;
    gap: 4rem;
    align-items: start;
  }

  .about-aside {
    padding-top: .4rem;
  }

  .about-numeral {
    font-family: var(--serif);
    font-size: 7rem;
    font-weight: 300;
    color: rgba(217,179,130,.08);
    line-height: 1;
    display: block;
    user-select: none;
  }

  .about-text h2 {
    font-family: var(--serif);
    font-size: clamp(1.8rem, 4vw, 2.8rem);
    font-weight: 300;
    line-height: 1.25;
    color: var(--ivory);
    margin-bottom: 1.6rem;
  }

  .about-text p {
    font-size: .95rem;
    color: var(--grey);
    line-height: 2;
    margin-bottom: 1.2rem;
  }

  .about-text p em { color: var(--ivory); font-style: italic; }

  /* ── QUOTE BAND ── */
  #quote-band {
    padding: 5rem 2rem;
    background: var(--bg2);
    position: relative;
    overflow: hidden;
  }
  #quote-band::before {
    content: '\201C';
    position: absolute;
    font-family: var(--serif);
    font-size: 28rem;
    color: rgba(217,179,130,.04);
    top: -4rem; left: -1rem;
    line-height: 1;
    pointer-events: none;
    user-select: none;
  }

  .quote-inner {
    max-width: 700px;
    margin: 0 auto;
    text-align: center;
  }

  .quote-text {
    font-family: var(--serif);
    font-size: clamp(1.4rem, 3vw, 2.1rem);
    font-style: italic;
    font-weight: 300;
    line-height: 1.55;
    color: var(--ivory);
    margin-bottom: 1.4rem;
  }

  .quote-attr {
    font-family: var(--serif);
    font-size: .82rem;
    letter-spacing: .22em;
    color: var(--gold);
    text-transform: uppercase;
  }

  /* ── FILTERS ── */
  .filter-bar {
    display: flex; flex-wrap: wrap; gap: .6rem;
    margin-bottom: 3.5rem;
  }
  .filter-btn {
    background: none;
    border: 1px solid rgba(217,179,130,.25);
    padding: .36rem 1.1rem;
    border-radius: 50px;
    font-family: var(--serif);
    font-size: .82rem;
    letter-spacing: .12em;
    color: var(--grey);
    cursor: pointer;
    transition: all .35s;
  }
  .filter-btn:hover { border-color: var(--gold); color: var(--gold); }
  .filter-btn.active {
    background: var(--gold-dim);
    border-color: var(--gold);
    color: var(--gold);
  }

  /* ── POETRY GRID ── */
  #poems { background: var(--bg); }

  .poems-header {
    display: flex; justify-content: space-between; align-items: flex-end;
    margin-bottom: 1rem;
  }
  .poems-header h2 {
    font-family: var(--serif);
    font-size: clamp(1.6rem, 3.5vw, 2.4rem);
    font-weight: 300;
    color: var(--ivory);
  }

  .poem-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 1.8rem;
  }

  .poem-card {
    background: rgba(255,255,255,.025);
    border: 1px solid rgba(217,179,130,.1);
    border-radius: 4px;
    padding: 2.2rem 1.8rem;
    cursor: pointer;
    transition: background .35s, border-color .35s, transform .35s;
    position: relative;
    overflow: hidden;
  }
  .poem-card::after {
    content: '';
    position: absolute; inset: 0;
    background: radial-gradient(ellipse at 50% 0%, rgba(217,179,130,.06) 0%, transparent 65%);
    opacity: 0;
    transition: opacity .4s;
  }
  .poem-card:hover {
    background: rgba(217,179,130,.05);
    border-color: rgba(217,179,130,.3);
    transform: translateY(-3px);
  }
  .poem-card:hover::after { opacity: 1; }

  .poem-tag {
    font-family: var(--serif);
    font-size: .72rem;
    letter-spacing: .22em;
    text-transform: uppercase;
    color: var(--gold);
    margin-bottom: .8rem;
    opacity: .7;
  }
  .poem-card h3 {
    font-family: var(--serif);
    font-size: 1.35rem;
    font-weight: 400;
    font-style: italic;
    color: var(--ivory);
    margin-bottom: .8rem;
    line-height: 1.3;
  }
  .poem-excerpt {
    font-family: var(--serif);
    font-size: .9rem;
    color: var(--grey);
    line-height: 1.9;
    font-style: italic;
  }
  .poem-read {
    display: inline-flex; align-items: center; gap: .4rem;
    margin-top: 1.2rem;
    font-family: var(--serif);
    font-size: .78rem;
    letter-spacing: .15em;
    text-transform: uppercase;
    color: var(--gold);
    opacity: .6;
    transition: opacity .3s, gap .3s;
  }
  .poem-card:hover .poem-read { opacity: 1; gap: .7rem; }

  /* ── FEATURED ── */
  #featured {
    background: linear-gradient(135deg, var(--bg2) 0%, #0f1322 50%, var(--bg2) 100%);
    position: relative;
  }
  #featured::before {
    content: '';
    position: absolute; inset: 0;
    background: radial-gradient(ellipse at 30% 50%, rgba(217,179,130,.04) 0%, transparent 60%);
    pointer-events: none;
  }

  .featured-header { margin-bottom: 3.5rem; }
  .featured-header h2 {
    font-family: var(--serif);
    font-size: clamp(1.6rem, 3.5vw, 2.4rem);
    font-weight: 300;
    color: var(--ivory);
  }

  .featured-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 2.5rem;
  }

  .featured-card {
    position: relative;
    padding: 3rem 2.6rem;
    border: 1px solid rgba(217,179,130,.18);
    cursor: pointer;
    overflow: hidden;
    transition: border-color .4s;
  }
  .featured-card::before {
    content: '';
    position: absolute; top: 0; left: 0; right: 0; height: 2px;
    background: linear-gradient(to right, transparent, var(--gold), transparent);
    opacity: .4;
    transition: opacity .4s;
  }
  .featured-card:hover { border-color: rgba(217,179,130,.4); }
  .featured-card:hover::before { opacity: 1; }

  .fc-number {
    font-family: var(--serif);
    font-size: .78rem;
    letter-spacing: .3em;
    color: var(--gold);
    opacity: .5;
    margin-bottom: 1rem;
  }
  .fc-title {
    font-family: var(--serif);
    font-size: clamp(1.5rem, 3vw, 2.1rem);
    font-weight: 300;
    font-style: italic;
    color: var(--ivory);
    margin-bottom: 1.4rem;
    line-height: 1.2;
  }
  .fc-lines {
    font-family: var(--serif);
    font-size: .95rem;
    font-style: italic;
    color: var(--grey);
    line-height: 2.2;
    border-left: 1px solid rgba(217,179,130,.22);
    padding-left: 1.2rem;
  }
  .fc-lines .line-gold { color: rgba(217,179,130,.7); }

  .fc-read-btn {
    display: inline-block;
    margin-top: 1.6rem;
    padding: .45rem 1.3rem;
    border: 1px solid rgba(217,179,130,.3);
    font-family: var(--serif);
    font-size: .8rem;
    letter-spacing: .16em;
    text-transform: uppercase;
    color: var(--gold);
    cursor: pointer;
    background: none;
    transition: background .35s, border-color .35s;
  }
  .fc-read-btn:hover {
    background: var(--gold-dim);
    border-color: var(--gold);
  }

  /* ── REFLECTIONS ── */
  #reflections { background: var(--bg); }
  .reflections-header { margin-bottom: 3.5rem; }
  .reflections-header h2 {
    font-family: var(--serif);
    font-size: clamp(1.6rem, 3.5vw, 2.4rem);
    font-weight: 300;
    color: var(--ivory);
  }

  .reflections-list {
    display: flex; flex-direction: column; gap: 0;
  }
  .reflection-item {
    padding: 2.4rem 0;
    border-bottom: 1px solid rgba(255,255,255,.05);
    display: grid;
    grid-template-columns: 48px 1fr;
    gap: 1.6rem;
    align-items: start;
    cursor: default;
    transition: background .3s;
  }
  .reflection-item:first-child { border-top: 1px solid rgba(255,255,255,.05); }
  .r-num {
    font-family: var(--serif);
    font-size: .78rem;
    color: rgba(217,179,130,.35);
    letter-spacing: .1em;
    padding-top: .25rem;
  }
  .r-text {
    font-family: var(--serif);
    font-size: clamp(1.05rem, 2.2vw, 1.4rem);
    font-style: italic;
    font-weight: 300;
    line-height: 1.65;
    color: var(--ivory);
  }
  .reflection-item:hover .r-text { color: var(--gold); }
  .reflection-item { transition: all .3s; }

  /* ── FOOTER ── */
  footer {
    background: var(--bg2);
    padding: 5rem 2rem 3rem;
    text-align: center;
    position: relative;
  }
  footer::before {
    content: '';
    position: absolute; top: 0; left: 50%; transform: translateX(-50%);
    width: 60px; height: 1px;
    background: linear-gradient(to right, transparent, var(--gold), transparent);
    opacity: .4;
  }

  .footer-sig {
    font-family: var(--serif);
    font-size: clamp(1.4rem, 3vw, 2rem);
    font-style: italic;
    font-weight: 300;
    color: var(--ivory);
    margin-bottom: .5rem;
  }
  .footer-tagline {
    font-family: var(--serif);
    font-size: .85rem;
    letter-spacing: .18em;
    color: var(--grey);
    text-transform: uppercase;
    margin-bottom: 2.8rem;
  }

  .footer-links {
    display: flex; justify-content: center; gap: 2rem;
    list-style: none;
    margin-bottom: 3rem;
  }
  .footer-links a {
    font-family: var(--serif);
    font-size: .82rem;
    letter-spacing: .15em;
    text-transform: uppercase;
    color: var(--grey);
    text-decoration: none;
    transition: color .35s;
  }
  .footer-links a:hover { color: var(--gold); }

  .footer-copy {
    font-size: .75rem;
    color: rgba(138,138,138,.4);
    letter-spacing: .08em;
  }

  /* ── POEM MODAL (focus mode) ── */
  .modal-overlay {
    position: fixed; inset: 0; z-index: 200;
    background: rgba(7,9,14,.97);
    display: flex; align-items: center; justify-content: center;
    padding: 2rem;
    opacity: 0;
    pointer-events: none;
    transition: opacity .5s ease;
  }
  .modal-overlay.open {
    opacity: 1;
    pointer-events: all;
  }

  .modal-inner {
    max-width: 560px;
    width: 100%;
    text-align: center;
    transform: translateY(20px);
    transition: transform .5s ease;
    position: relative;
  }
  .modal-overlay.open .modal-inner { transform: translateY(0); }

  .modal-tag {
    font-family: var(--serif);
    font-size: .75rem;
    letter-spacing: .3em;
    text-transform: uppercase;
    color: var(--gold);
    opacity: .6;
    margin-bottom: 1.2rem;
  }
  .modal-title {
    font-family: var(--serif);
    font-size: clamp(1.8rem, 4vw, 2.8rem);
    font-weight: 300;
    font-style: italic;
    color: var(--ivory);
    margin-bottom: 2.4rem;
    line-height: 1.2;
  }
  .modal-rule {
    width: 40px; height: 1px;
    background: linear-gradient(to right, transparent, var(--gold), transparent);
    margin: 0 auto 2.4rem;
  }
  .modal-poem {
    font-family: var(--serif);
    font-size: 1.05rem;
    font-style: italic;
    color: rgba(245,243,235,.85);
    line-height: 2.4;
    white-space: pre-line;
  }
  .modal-close {
    position: absolute; top: -3rem; right: 0;
    background: none; border: none;
    font-family: var(--serif);
    font-size: .78rem;
    letter-spacing: .22em;
    text-transform: uppercase;
    color: var(--grey);
    cursor: pointer;
    transition: color .3s;
  }
  .modal-close:hover { color: var(--gold); }

  /* ── SCROLL REVEAL ── */
  .reveal {
    opacity: 0;
    transform: translateY(28px);
    transition: opacity .9s ease, transform .9s ease;
  }
  .reveal.visible { opacity: 1; transform: none; }
  .reveal-delay-1 { transition-delay: .1s; }
  .reveal-delay-2 { transition-delay: .2s; }
  .reveal-delay-3 { transition-delay: .32s; }
  .reveal-delay-4 { transition-delay: .44s; }

  /* ── RESPONSIVE ── */
  @media (max-width: 720px) {
    nav { padding: 1.2rem 1.4rem; }
    .nav-links { gap: 1.2rem; }
    .about-inner { grid-template-columns: 1fr; gap: 2rem; }
    .about-numeral { font-size: 4rem; }
    .featured-grid { grid-template-columns: 1fr; }
    .poem-grid { grid-template-columns: 1fr; }
    .reflection-item { grid-template-columns: 1fr; gap: .4rem; }
    .r-num { display: none; }
    footer { padding: 4rem 1.4rem 2.5rem; }
  }
  @media (max-width: 480px) {
    .nav-links { display: none; }
  }
</style>
</head>
<body>

<!-- NAV -->
<nav>
  <a href="#hero" class="nav-brand">The Wandering Verse</a>
  <ul class="nav-links">
    <li><a href="#about">About</a></li>
    <li><a href="#poems">Poems</a></li>
    <li><a href="#featured">Featured</a></li>
    <li><a href="#reflections">Reflections</a></li>
  </ul>
  <button class="sound-btn" id="soundBtn" title="Toggle ambient sound" aria-label="Toggle sound">
    ♩
  </button>
</nav>

<!-- HERO -->
<section id="hero">
  <canvas id="sky-canvas"></canvas>
  <div class="hero-veil"></div>
  <div class="hero-content">
    <span class="hero-eyebrow">Poetry &amp; Reflection</span>
    <h1 class="hero-title">The <em>Wandering</em><br>Verse</h1>
    <div class="hero-rule"></div>
    <p class="hero-sub">For the quiet moments when the mind begins to wander.</p>
  </div>
  <div class="hero-scroll">
    <span>Scroll</span>
    <div class="scroll-line"></div>
  </div>
</section>

<!-- ABOUT -->
<section id="about">
  <div class="container">
    <div class="about-inner">
      <div class="about-aside reveal">
        <span class="about-numeral">I</span>
      </div>
      <div class="about-text reveal reveal-delay-1">
        <span class="s-label">About</span>
        <h2>A quiet space for wandering thoughts.</h2>
        <p>The Wandering Verse is a place where words settle slowly, like dust in evening light. These poems are born from <em>small moments</em> — silence between conversations, the weight of an unsent letter, the strange peace of 2AM.</p>
        <p>It is not about loud expression, but <em>quiet understanding</em>. Not performance, but presence. Each line written in the hope that someone, somewhere, will read it and feel less alone.</p>
        <p>Here, connection begins in the space between words — <em>in what is left unsaid.</em></p>
      </div>
    </div>
  </div>
</section>

<!-- QUOTE BAND -->
<div id="quote-band">
  <div class="quote-inner reveal">
    <p class="quote-text">"When a person is tired, they don't seek knowledge, but peace."</p>
    <span class="quote-attr">— The Wandering Verse</span>
  </div>
</div>

<!-- POEMS -->
<section id="poems">
  <div class="container-wide">
    <div class="poems-header reveal">
      <div>
        <span class="s-label">The Collection</span>
        <h2>All Poems</h2>
      </div>
    </div>

    <div class="filter-bar reveal reveal-delay-1">
      <button class="filter-btn active" data-filter="all">All</button>
      <button class="filter-btn" data-filter="Silence">Silence</button>
      <button class="filter-btn" data-filter="Love">Love</button>
      <button class="filter-btn" data-filter="Growth">Growth</button>
      <button class="filter-btn" data-filter="Misunderstandings">Misunderstandings</button>
    </div>

    <div class="poem-grid" id="poemGrid"></div>
  </div>
</section>

<!-- FEATURED -->
<section id="featured">
  <div class="container-wide">
    <div class="featured-header reveal">
      <span class="s-label">Featured Works</span>
      <h2>Poems to linger with.</h2>
    </div>
    <div class="featured-grid">

      <div class="featured-card reveal reveal-delay-1" data-poem="0">
        <div class="fc-number">01</div>
        <div class="fc-title">The Small Room</div>
        <div class="fc-lines">
          <div>There is a room I return to</div>
          <div>every time I am afraid —</div>
          <div class="line-gold">small and warm and always there,</div>
          <div>a place that doesn't need my name.</div>
        </div>
        <button class="fc-read-btn">Read in Full →</button>
      </div>

      <div class="featured-card reveal reveal-delay-2" data-poem="1">
        <div class="fc-number">02</div>
        <div class="fc-title">Letters Across the Sea</div>
        <div class="fc-lines">
          <div>I have written you a hundred letters</div>
          <div>and sent you none of them.</div>
          <div class="line-gold">They live in the drawer beside my bed,</div>
          <div>quiet as water, patient as stones.</div>
        </div>
        <button class="fc-read-btn">Read in Full →</button>
      </div>

    </div>
  </div>
</section>

<!-- REFLECTIONS -->
<section id="reflections">
  <div class="container">
    <div class="reflections-header reveal">
      <span class="s-label">Reflections</span>
      <h2>Lines to carry with you.</h2>
    </div>
    <div class="reflections-list reveal reveal-delay-1">
      <div class="reflection-item">
        <span class="r-num">—</span>
        <p class="r-text">"When a person is tired, they don't seek knowledge, but peace."</p>
      </div>
      <div class="reflection-item">
        <span class="r-num">—</span>
        <p class="r-text">"The most honest conversations happen after midnight, when the pretending stops."</p>
      </div>
      <div class="reflection-item">
        <span class="r-num">—</span>
        <p class="r-text">"Some silences are not empty — they are full of the things we never found words for."</p>
      </div>
      <div class="reflection-item">
        <span class="r-num">—</span>
        <p class="r-text">"Growing is not always loud. Sometimes it is the quiet decision to stay, to try once more."</p>
      </div>
      <div class="reflection-item">
        <span class="r-num">—</span>
        <p class="r-text">"We are all just wandering, hoping someone will recognize us from across the dark."</p>
      </div>
      <div class="reflection-item">
        <span class="r-num">—</span>
        <p class="r-text">"To be understood is the closest thing to being loved without condition."</p>
      </div>
    </div>
  </div>
</section>

<!-- FOOTER -->
<footer>
  <p class="footer-sig">— The Wandering Verse</p>
  <p class="footer-tagline">for wandering minds</p>
  <ul class="footer-links">
    <li><a href="#">Instagram</a></li>
    <li><a href="#">Email</a></li>
    <li><a href="#">Archive</a></li>
  </ul>
  <p class="footer-copy">© 2024 The Wandering Verse · Quietly crafted.</p>
</footer>

<!-- POEM MODAL -->
<div class="modal-overlay" id="poemModal">
  <div class="modal-inner">
    <button class="modal-close" id="modalClose">✕ Close</button>
    <div class="modal-tag" id="modalTag"></div>
    <h2 class="modal-title" id="modalTitle"></h2>
    <div class="modal-rule"></div>
    <div class="modal-poem" id="modalPoem"></div>
  </div>
</div>

<script>
/* ───────── POEM DATA ───────── */
const poems = [
  {
    title: "The Small Room",
    tag: "Silence",
    excerpt: "There is a room I return to\nevery time I am afraid —",
    full: `There is a room I return to
every time I am afraid —
small and warm and always there,
a place that doesn't need my name.

No windows face the street here.
The light is old and amber-soft.
I never learned how I arrived,
only that the door was not locked.

I think this room is memory.
I think this room is you.
I think the reason I keep coming back
is that I don't know what else to do.`
  },
  {
    title: "Letters Across the Sea",
    tag: "Love",
    excerpt: "I have written you a hundred letters\nand sent you none of them.",
    full: `I have written you a hundred letters
and sent you none of them.
They live in the drawer beside my bed,
quiet as water, patient as stones.

One says: I was wrong.
One says: I am still waiting.
One says only your name,
repeated until it stopped meaning anything
and then started again.

The sea between us is not water.
It is the silence after speaking,
the phone set face-down,
the door closed without slamming.

If you ever find these letters,
please know I meant all of them.
Even the ones that contradict.
Especially those.`
  },
  {
    title: "What Silence Holds",
    tag: "Silence",
    excerpt: "Not every quiet is empty.\nSome silences are full of old names.",
    full: `Not every quiet is empty.
Some silences are full of old names,
old rooms, old versions of yourself
you left in doorways of the past.

I have learned to listen
to the space between the words —
where the real conversation lives,
unhurried, unafraid.

At 2AM the world goes still
and I can finally hear it:
the low hum of what matters,
the soft breath of what remains.`
  },
  {
    title: "The Distance Between Us",
    tag: "Misunderstandings",
    excerpt: "We were speaking the same language\nfrom different dictionaries.",
    full: `We were speaking the same language
from different dictionaries.

The word you called love
was the word I called need.
The word I called patience
was the word you called absence.

I wonder sometimes
if we ever truly spoke at all,
or only exchanged familiar sounds
and filled the rest in ourselves.

Perhaps that is all conversation is.
Perhaps that is enough.`
  },
  {
    title: "Slow Becoming",
    tag: "Growth",
    excerpt: "I did not change all at once.\nI changed the way rivers change —",
    full: `I did not change all at once.
I changed the way rivers change —
quietly, over stone,
over many unremarkable seasons.

First the sharp edges softened.
Then the noise began to settle.
Then I found I could sit in a room
without needing to fill it.

I call this growing up,
though it felt more like arriving —
like coming home to a house
I had not yet known was mine.`
  },
  {
    title: "An Ordinary Evening",
    tag: "Silence",
    excerpt: "Nothing happened tonight.\nAnd I am learning to be glad of that.",
    full: `Nothing happened tonight.
And I am learning to be glad of that.

The tea was warm. The street was quiet.
A candle burned down to its last inch.
Somewhere a door opened, then closed,
and the night accepted it without comment.

This is the life I used to call ordinary.
Now I think ordinary
is just extraordinary slowed down —
made patient, made possible.`
  },
  {
    title: "The Weight of Almost",
    tag: "Love",
    excerpt: "There are loves that never quite arrive.\nThey circle like birds above a city,",
    full: `There are loves that never quite arrive.
They circle like birds above a city,
looking for somewhere to land,
finding nothing, moving on.

You were one of those almost-loves.
Not a failure — something finer:
a door that stayed a little open,
light along the floor at night.

I keep you not as a wound
but as a kind of wondering —
what we might have been
in a world that gave us more time.`
  },
  {
    title: "On Being Misunderstood",
    tag: "Misunderstandings",
    excerpt: "Sometimes I speak and the words arrive\ncorrectly, but without me in them.",
    full: `Sometimes I speak and the words arrive
correctly, but without me in them.
Like sending a letter
inside someone else's handwriting.

I have learned not to blame the distance.
Language is a long road.
Meaning gets tired, sits down somewhere
in the middle, doesn't quite finish.

Still I try. Still I reach across.
Because the only thing worse than being misunderstood
is never trying to be understood at all.`
  }
];

/* ───────── RENDER POEMS ───────── */
const grid = document.getElementById('poemGrid');
let activeFilter = 'all';

function renderPoems(filter) {
  grid.innerHTML = '';
  const filtered = filter === 'all' ? poems : poems.filter(p => p.tag === filter);
  filtered.forEach((p, i) => {
    const card = document.createElement('div');
    card.className = 'poem-card reveal';
    card.style.transitionDelay = `${i * 0.07}s`;
    card.innerHTML = `
      <div class="poem-tag">${p.tag}</div>
      <h3>${p.title}</h3>
      <p class="poem-excerpt">${p.excerpt}</p>
      <span class="poem-read">Read → </span>
    `;
    card.addEventListener('click', () => openModal(p));
    grid.appendChild(card);
    requestAnimationFrame(() => requestAnimationFrame(() => {
      if (card.getBoundingClientRect().top < window.innerHeight + 100) {
        card.classList.add('visible');
      }
    }));
  });
  observeReveal();
}

renderPoems('all');

/* filter buttons */
document.querySelectorAll('.filter-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    activeFilter = btn.dataset.filter;
    renderPoems(activeFilter);
  });
});

/* featured card clicks */
document.querySelectorAll('.featured-card').forEach(card => {
  card.addEventListener('click', () => {
    const idx = parseInt(card.dataset.poem);
    openModal(poems[idx]);
  });
});

/* ───────── MODAL ───────── */
const modal = document.getElementById('poemModal');
const modalClose = document.getElementById('modalClose');

function openModal(poem) {
  document.getElementById('modalTag').textContent = poem.tag;
  document.getElementById('modalTitle').textContent = poem.title;
  document.getElementById('modalPoem').textContent = poem.full;
  modal.classList.add('open');
  document.body.style.overflow = 'hidden';
}
function closeModal() {
  modal.classList.remove('open');
  document.body.style.overflow = '';
}
modalClose.addEventListener('click', closeModal);
modal.addEventListener('click', e => { if (e.target === modal) closeModal(); });
document.addEventListener('keydown', e => { if (e.key === 'Escape') closeModal(); });

/* ───────── SCROLL REVEAL ───────── */
function observeReveal() {
  const els = document.querySelectorAll('.reveal:not(.visible)');
  const io = new IntersectionObserver((entries) => {
    entries.forEach(e => {
      if (e.isIntersecting) { e.target.classList.add('visible'); io.unobserve(e.target); }
    });
  }, { threshold: 0.12 });
  els.forEach(el => io.observe(el));
}
observeReveal();

/* ───────── NIGHT SKY CANVAS ───────── */
const canvas = document.getElementById('sky-canvas');
const ctx = canvas.getContext('2d');
let W, H, stars = [], driftX = 0, driftY = 0;

function resize() {
  W = canvas.width = canvas.offsetWidth;
  H = canvas.height = canvas.offsetHeight;
}
resize();
window.addEventListener('resize', resize);

function initStars() {
  stars = [];
  const count = Math.min(Math.floor((W * H) / 4000), 280);
  for (let i = 0; i < count; i++) {
    stars.push({
      x: Math.random() * W,
      y: Math.random() * H,
      r: Math.random() * 1.1 + .2,
      alpha: Math.random() * .7 + .15,
      speed: Math.random() * .0004 + .0002,
      phase: Math.random() * Math.PI * 2,
    });
  }
}
initStars();
window.addEventListener('resize', initStars);

/* parallax */
document.addEventListener('mousemove', e => {
  driftX = (e.clientX / window.innerWidth - .5) * 6;
  driftY = (e.clientY / window.innerHeight - .5) * 4;
});

let t = 0;
function drawSky() {
  ctx.clearRect(0, 0, W, H);

  /* soft gradient bg */
  const grd = ctx.createRadialGradient(W*.5, H*.35, 0, W*.5, H*.5, H*.85);
  grd.addColorStop(0, 'rgba(30,40,80,.55)');
  grd.addColorStop(.6, 'rgba(11,15,26,.25)');
  grd.addColorStop(1, 'rgba(11,15,26,0)');
  ctx.fillStyle = grd;
  ctx.fillRect(0, 0, W, H);

  /* moon glow */
  const mx = W * .72 + driftX * .6, my = H * .18 + driftY * .4;
  const mg = ctx.createRadialGradient(mx, my, 0, mx, my, 110);
  mg.addColorStop(0, 'rgba(217,200,160,.12)');
  mg.addColorStop(.4, 'rgba(217,190,130,.04)');
  mg.addColorStop(1, 'rgba(217,190,130,0)');
  ctx.fillStyle = mg;
  ctx.beginPath(); ctx.arc(mx, my, 110, 0, Math.PI*2); ctx.fill();
  /* moon disc */
  const md = ctx.createRadialGradient(mx-4, my-4, 0, mx, my, 18);
  md.addColorStop(0, 'rgba(245,238,215,.55)');
  md.addColorStop(.5, 'rgba(230,215,170,.22)');
  md.addColorStop(1, 'rgba(217,190,130,0)');
  ctx.fillStyle = md;
  ctx.beginPath(); ctx.arc(mx, my, 18, 0, Math.PI*2); ctx.fill();

  /* stars */
  t += .008;
  stars.forEach(s => {
    const pulse = .5 + .5 * Math.sin(t * s.speed * 600 + s.phase);
    const a = s.alpha * (.55 + .45 * pulse);
    const sx = s.x + driftX * (s.r * .5);
    const sy = s.y + driftY * (s.r * .4);
    ctx.beginPath();
    ctx.arc(sx, sy, s.r, 0, Math.PI*2);
    ctx.fillStyle = `rgba(245,243,235,${a})`;
    ctx.fill();
  });

  requestAnimationFrame(drawSky);
}
drawSky();

/* ───────── SOUND TOGGLE ───────── */
const soundBtn = document.getElementById('soundBtn');
let audioCtx = null, gainNode = null, soundOn = false;

function buildNight() {
  audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  gainNode = audioCtx.createGain();
  gainNode.gain.value = 0;
  gainNode.connect(audioCtx.destination);

  /* gentle low hum / ambient pad */
  [55, 110, 165].forEach((freq, i) => {
    const osc = audioCtx.createOscillator();
    const g = audioCtx.createGain();
    osc.type = i === 0 ? 'sine' : 'triangle';
    osc.frequency.value = freq;
    g.gain.value = i === 0 ? .06 : .02;
    osc.connect(g); g.connect(gainNode);
    osc.start();
  });

  /* soft noise (wind) */
  const bufLen = audioCtx.sampleRate * 3;
  const buf = audioCtx.createBuffer(1, bufLen, audioCtx.sampleRate);
  const data = buf.getChannelData(0);
  for (let i = 0; i < bufLen; i++) data[i] = (Math.random() * 2 - 1) * .015;
  const src = audioCtx.createBufferSource();
  src.buffer = buf; src.loop = true;
  const filt = audioCtx.createBiquadFilter();
  filt.type = 'lowpass'; filt.frequency.value = 400;
  src.connect(filt); filt.connect(gainNode); src.start();
}

soundBtn.addEventListener('click', () => {
  if (!audioCtx) buildNight();
  soundOn = !soundOn;
  gainNode.gain.cancelScheduledValues(audioCtx.currentTime);
  gainNode.gain.linearRampToValueAtTime(soundOn ? .4 : 0, audioCtx.currentTime + 1.5);
  soundBtn.classList.toggle('active', soundOn);
  soundBtn.textContent = soundOn ? '♪' : '♩';
  if (soundOn && audioCtx.state === 'suspended') audioCtx.resume();
});
</script>
</body>
</html>
