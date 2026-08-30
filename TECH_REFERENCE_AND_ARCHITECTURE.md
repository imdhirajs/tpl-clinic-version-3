# TPL Clinic (The PRP Lab) — Technical Architecture & Reference Guide (Version 3)

**Project Name:** TPL Clinic (The PRP Lab) — Mayfair, London  
**Live Production URL (Netlify V3):** [https://tpl-clinic-version-3.netlify.app](https://tpl-clinic-version-3.netlify.app)  
**GitHub Repository (V3):** [https://github.com/imdhirajs/tpl-clinic-version-3.git](https://github.com/imdhirajs/tpl-clinic-version-3.git) (Branch: `main`)  
**Previous Reference (V2):** [https://tpl-clinic-version-2.netlify.app](https://tpl-clinic-version-2.netlify.app)  
**Master Source:** `Hero_V8.mp4` (8K Ultra HD master @ 7680×4320)  
**Last Updated:** August 30, 2026

---

## 🏛️ Executive Summary & Technology Overview

Version 3 delivers **crystal-clear, full HD / 4K medical-grade visual clarity** across all 1,000 frames of the signature arrival sequence. Every frame was extracted directly from the 8K `Hero_V8.mp4` master video into 250 high-definition 2×2 WebP spritesheets (3840×2160 per sheet).

---

## 🛠️ Complete Technology Stack & Specifications

### 1. Master 8K Frame Extraction & Canvas Pipeline
* **Source:** `Hero_V8.mp4` (7680×4320 8K Ultra HD, 1,011 frames).
* **Grid Format:** 250 master 2×2 WebP sheets (`sprite-v3-001.webp` through `sprite-v3-250.webp`), each 3840×2160 (4 Full HD 1920×1080 frames per sheet).
* **Storage & Edge Delivery:** Deployed directly to Netlify's high-speed global edge network (`assets/hero-sprites-v3/`) with HTTP/2 and HTTP/3 multiplexing.
* **Dynamic Sprite Slicing (`drawSingleFrame`)**:
  ```javascript
  const nw = spriteImg.naturalWidth || spriteImg.width || 3840;
  const nh = spriteImg.naturalHeight || spriteImg.height || 2160;
  const cellW = nw / 2;
  const cellH = nh / 2;
  const sx = cellCol * cellW;
  const sy = cellRow * cellH;
  ctx.drawImage(spriteImg, sx, sy, cellW, cellH, offsetX, offsetY, drawW, drawH);
  ```

### 2. Preloader Runway & Parallel Background Streamer
* **Method 08 Progressive Preloader**:
  * Blocks scroll until the first 50 sheets (200 frames) are 100% downloaded and pre-decoded into memory.
  * Real-time visual progress bar (`#tpl-loading-bar`).
* **8-Worker Parallel Background Streamer**:
  * Immediately after preloader unlocks, 8 parallel worker streams fetch the remaining 200 sheets over HTTP/2 within ~2 seconds.
* **Zero-Eviction Memory Buffer (`MAX_SPRITES = 250`)**:
  * Holds all 250 sheets in memory (~500 MB RAM budget) ensuring zero stutters or re-fetches during forward and backward scrubbing.

### 3. Single-Loop Smooth Scroll Engine
* **Lenis Virtual Scroll (`lenis@1.1.18`)**:
  * Physics-based smooth inertia: `duration: 1.2`, `easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t))`.
* **GSAP Ticker Integration (`gsap.ticker`)**:
  * Unified single-loop rendering: `gsap.ticker.add((time) => lenis.raf(time * 1000)); gsap.ticker.lagSmoothing(0);`

### 4. Multi-Page Architecture & Route Map
* `index.html` — Signature 1,000-frame hero, beat-synced hotspot cards, philosophy section, technology index, patient reviews, and booking modal.
* `prp.html` — Dedicated Platelet Rich Plasma signature deep-dive.
* `sofwave.html` — Sofwave™ Ultrasound Lifting treatment page.
* `services.html` — Master 20-treatment clinical index with interactive category filter pills.
* `about.html` — Clinic presence at 10 Harley Street Mayfair & practitioner team.
* `terms-conditions.html` & `gdpr.html` — Medical compliance & privacy policies.
