# Google Summer of Code 2025 – Final Report

<!-- markdownlint-disable MD033 -->
<a href="https://summerofcode.withgoogle.com/">
  <img src="assets/gsoc-w.svg" alt="GSoC" width="512"/>
</a>
<br><br>
<!-- markdownlint-enable MD033 -->

**Contributor:** [Mohamed Hany Youns](https://www.linkedin.com/in/mohamedhany01/)

**Organization:** [Chromium](https://www.chromium.org/Home/)

**Project Title:** [Enhancing INP Insights for Developers](https://docs.google.com/document/d/1TpF0qrtYqTYnBjWsgt65_bLaNQ02fIosYxMN7HWuwQQ)

**Mentors:** [Michal Mocny](mmocny@google.com), [Annie Sullivan](sullivan@google.com), [Johannes Henkel](johannes@chromium.org), [Scott Haseley](shaseley@chromium.org)

**GSoC Proposal Page:** [Proposal Document](https://docs.google.com/document/d/1iDVZYM9R0dVbK9by8URSCtd3_aHA5BilpJXjJ7z4Ox0)

**Topics:** Browser Engineering, Browser Internals, Web Performance, DevTools, Web Vitals, Speed Metrics

**Technologies:** C++, JavaScript, HTML, CSS, GTest(GoogleTest)

---

## Table of Contents

- [Project Overview](#project-overview)
- [Work Completed](#work-completed)
  - [Deliverables & Status](#deliverables--status)
  - [Why it matters](#why-it-matters)
- [Challenges & Lessons Learned](#challenges--lessons-learned)
  - [Challenges](#challenges)
  - [Lessons Learned](#lessons-learned)
- [Acknowledgements](#acknowledgements)
- [Resources](#resources)
  - [Specifications & Standards](#specifications--standards)
  - [Chromium & Performance Engineering](#chromium--performance-engineering)
  - [Tooling & User Experience Data](#tooling--user-experience-data)
  - [Browser Internals (Deep Dive)](#browser-internals-deep-dive)
  - [Extra](#extra)

---

## Project Overview

<!-- markdownlint-disable MD033 -->
<a href="https://www.chromium.org/Home/">
  <img src="assets/chromium.svg" alt="Chromium" width="120"/>
</a>
<br><br>
<!-- markdownlint-enable MD033 -->

Chromium, as an open-source browser that powers major browsers like Google Chrome and Microsoft Edge, plays a critical role in shaping web standards and ensuring performance metrics are consistent across the web. By advancing INP observability in Chromium, this project strengthens the ecosystem of performance tooling, directly impacting how developers optimize responsiveness and ultimately improving user experiences across billions of devices worldwide.

The goal of this project was to enhance the [**EventTiming**](https://www.w3.org/TR/event-timing/), [**PaintTiming**](https://www.w3.org/TR/paint-timing/), and [`PerformanceEventTiming`](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceEventTiming) APIs. These APIs are the foundation for [**Interaction to Next Paint (INP)**](https://web.dev/articles/inp), a Core Web Vital that measures website responsiveness. By providing richer, more contextual data, this work improves both the developer experience (DevEx) with more powerful diagnostic tools and the user experience (UX) with faster, smoother websites.

These enhancements are now available to be exposed in high-level tools like DevTools, Lighthouse, and PageSpeed Insights, helping developers analyze and fix responsiveness issues more effectively. For everyday users, this means smoother, faster interactions as pages respond more quickly to clicks, taps, and typing.

---

## Work Completed

### Deliverables & Status

**[Request presentation time only when painting](https://chromium-review.googlesource.com/c/chromium/src/+/6760194) — 🟡 Under Review**

Optimized EventTiming by aligning presentation time strictly with real paints, eliminating redundant callbacks and reducing performance overhead. This gave developers more accurate metrics while ensuring site owners received reliable data in tools like Lighthouse and CrUX.

---

**[Added fallback reason & timing](https://chromium-review.googlesource.com/c/chromium/src/+/6614684) — ✅ Merged**

Extended EventTiming to capture *when* and *why* slow interactions fell back (e.g., visibility change, modal dialog, unexpected frame). This made the root cause of delays immediately visible in traces, speeding up debugging and reducing engineering effort.

---

**[New trace event: `MeasurementComplete`](https://chromium-review.googlesource.com/c/chromium/src/+/6652835) — ✅ Merged**

Added a trace marker to pinpoint when EventTiming data was reported, bridging the gap between GPU presentation and timeline reporting. This helped developers quickly spot bottlenecks and optimize responsiveness more efficiently.

---

**[Simplified click tracking](https://chromium-review.googlesource.com/c/chromium/src/+/6700152) — ✅ Merged**

Simplified click interaction logic by removing obsolete histograms and trusting `pointer_id` across all click sources. This reduced complexity, improved reliability, and lowered the chance of future bugs.

---

**[Removed old keyboard-click code](https://chromium-review.googlesource.com/c/chromium/src/+/6662558) — ✅ Merged**

Deleted obsolete runtime checks for keyboard click handling, since the feature was already fully launched. This cleanup reduced technical debt and kept Chromium easier to maintain.

---

**[Removed old selection auto-scroll code](https://chromium-review.googlesource.com/c/chromium/src/+/6663358) — ✅ Merged**

Cleaned up unused runtime logic for selection auto-scroll, which was already stable and permanently enabled. This kept the codebase leaner and more sustainable for future development.

### Why it matters (Impact)

My work helps developers/engineers find and fix what makes websites slow. This means a faster web experience for everyone.

- **For Developers: Finding problems is now easier**
  - **See if the problem is code logic or painting:** Before, it was hard to tell if the code was slow or if the browser was slow to show the update. Now, with new timers like `paintTime`, developers know exactly where to look.
  - **Find the exact reason for a delay:** Before, developers knew a page was slow but often couldn't see why. Now, they can see the specific cause, like a pop-up window getting in the way.
  - **Make developer tools more accurate:** I added a new trace marker that shows exactly when a measurement is finished. This helps make performance tools more reliable.

- **For Users: Less waiting, more doing**
  - When developers can easily fix slowness, websites feel faster. When you click a button, the page responds instantly, without that frustrating lag.

**Note:** All contributions are available on [Chromium Gerrit](https://chromium-review.googlesource.com/q/owner:mohamedhyouns@gmail.com), including submitted patches, reviews, and merged changes from this project.

---

## Challenges & Lessons Learned

### Challenges

- **Scale of Chromium:** Navigating a codebase with millions of lines across C++, Blink, and rendering pipelines made it difficult to trace how input events flow through to responsiveness metrics.

- **System-wide complexity:** INP and EventTiming span multiple subsystems (input, rendering, compositing, metrics), so changes required careful cross-component understanding rather than isolated edits.

- **Ensuring reliability:** New tracing and observability features had to be both accurate and low-overhead, balancing developer visibility with end-user performance.

- **Maintaining stability:** Cleaning up legacy paths and simplifying event tracking demanded caution to avoid regressions for accessibility or edge cases.
- **Heavy build process:** Even with [GOMA](https://chromium.googlesource.com/infra/goma/client/+/ac9d3edd78849599d1fa5db65992e768ba0568ac/README.md) and distributed builds in the cloud, full Chromium builds were long and resource-intensive, slowing down iteration cycles.

- **Challenging debugging workflow:** Debugging required a combination of tracing, Perfetto UI, DevTools, and custom instrumentation, often across multiple iterations to isolate subtle timing issues.

- **Long review process:** Because these were **critical, user-facing performance metrics**, every change required multiple senior reviews, detailed justifications, and iteration before acceptance.

### Lessons Learned

- **Software engineering & collaboration best practices:** Improved at writing clean commits, maintaining clear documentation, and handling review feedback effectively, while also sharpening skills in design docs, trade-off discussions, and clear technical communication.

- **End-to-end SDLC exposure:** Worked across the full development lifecycle, from clarifying high-level, often ambiguous requirements, to adding new features, fixing bugs, optimizing performance, updating/fixing tests, refactoring legacy code, and cleaning up unused logic. This experience strengthened my ability to deal with uncertainty while still delivering reliable outcomes.

- **Browser engineering & internals:** Gained hands-on experience in a critical, complex, and niche domain where few engineers get direct exposure. Worked inside Chromium’s latency pipeline, Perfetto tracing, and Core Web Vitals, areas that directly influence web standards, SEO, and user experience for billions of users worldwide.

- **Learning from experts:** Benefited from close collaboration with the Chrome Speed Metrics team at Google, learning from their mentorship, design discussions, and detailed reviews. This helped me grow a stronger professional engineering mindset and higher standards for quality.

---

## Acknowledgments

I am deeply grateful to my mentors **Michal Mocny, Annie Sullivan, Johannes Henkel, and Scott Haseley** for their guidance throughout this project. Michal helped me navigate the goals and codebase and provided thoughtful reviews, Annie offered valuable big-picture insights, while Johannes and Scott’s detailed reviews significantly sharpened the quality of my contributions.

I also want to thank the **GSoC organizers and admins**: Sreeja, Stephanie, and Daisuke for their constant support in making this program a smooth and rewarding experience.

---

## Resources

Throughout this project, I relied on a mix of specifications, technical documentation, and deep-dive articles to guide development and better understand browser internals. Below are the most valuable references for anyone interested in exploring the same areas.

### Specifications & Standards

- [Event Timing API (W3C Engineering Spec)](https://www.w3.org/TR/event-timing/)
- [Interaction to Next Paint (INP) explainer](https://web.dev/articles/inp)
- [Core Web Vitals overview](https://web.dev/articles/vitals)

### Chromium & Performance Engineering

- [Chromium Speed Metrics Team Docs](https://chromium.googlesource.com/chromium/src/+/main/docs/speed_metrics/README.md)
- [Chromium Code Search](https://source.chromium.org/chromium)
- [Perfetto Tracing Docs](https://perfetto.dev/docs/)

### Tooling & User Experience Data

- [Chrome User Experience Report (CrUX)](https://developer.chrome.com/docs/crux)
- [Lighthouse Documentation](https://developer.chrome.com/docs/lighthouse)

### Browser Internals (Deep Dive)

- **Inside look at modern web browsers (4-part series):** [Part 1](https://developer.chrome.com/blog/inside-browser-part1), [Part 2](https://developer.chrome.com/blog/inside-browser-part2), [Part 3](https://developer.chrome.com/blog/inside-browser-part3), [Part 4](https://developer.chrome.com/blog/inside-browser-part4)

- [How modern browsers work](https://addyo.substack.com/p/how-modern-browsers-work)

- [Digging into Interaction to Next Paint (INP)](https://www.youtube.com/watch?v=bDJB-AQDciE)

- [Lessons Learned from Performance Monitoring in Chrome](https://www.youtube.com/watch?v=ctavZT87syI)

- [BlinkOn Conference Videos](https://www.youtube.com/@blinkontalks/videos)

### Extra

- [Chromium Show and Tell Presentation](https://docs.google.com/presentation/d/1dBT0vUTCuG2IC8-vesf-RVF0p1Pb7MM4UaoydhgQ-Lg)
