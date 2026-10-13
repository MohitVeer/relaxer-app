## Relaxer

A guided breathing exercise: a short repeating cycle of "Breathe In!", "Hold", and "Breathe Out!", paired with a circle that visibly grows and shrinks and a small dot that orbits around it in sync.

## Overview

Everything lives inside one `.container` div: a solid circle, a conic-gradient ring behind it, an orbiting pointer dot, and a text label. JavaScript only ever does two things — swap the label text and swap the container's class name — and CSS keyframe animations do the actual growing, shrinking, and rotating.

## Why I Built It

The interesting part here is keeping a JS timer sequence and independent CSS animations lined up: the text has to change at the same moments the circle starts growing or shrinking, without JS driving the animation itself frame by frame.

## Features

- A 7.5-second cycle: "Breathe In!" for 3s while the circle grows, "Hold" for 1.5s, then "Breathe Out!" for 3s while the circle shrinks back down
- A pointer dot that continuously rotates around the circle on its own independent animation, giving a constant sense of motion between phases
- The breathing phase text is marked `aria-live="polite"`, so screen readers announce each phase change as it happens rather than only when the page is first read

## JavaScript Concepts

- **Nested `setTimeout` calls** — the "Hold" and "Breathe Out!" phases are scheduled as timeouts nested inside the previous phase's timeout, so each step only fires after the right delay from the step before it
- **`setInterval` for repetition** — the whole three-phase sequence is wrapped in a function that `setInterval` re-runs every `totalTime` milliseconds
- **`className` reassignment over `classList`** — setting `container.className = 'container grow'` directly (rather than adding/removing classes) replaces the previous animation class outright, which restarts the CSS animation cleanly on each cycle

## How It Works

`breathAnimation` sets the text to "Breathe In!" and assigns the `grow` class, which triggers a 3-second CSS scale animation. A `setTimeout` for `breatheTime` (3s) then switches the text to "Hold"; a second, nested `setTimeout` for `holdTime` (1.5s) after that switches the text to "Breathe Out!" and swaps in the `shrink` class. An outer `setInterval` calls `breathAnimation` again every `totalTime` (7.5s) to repeat the cycle. The orbiting dot isn't driven by any of this — it's a separate, always-running CSS animation on `.pointer-container` that happens to share the same 7.5s duration, so it stays visually in sync with the breathing cycle without any JS coordinating it directly.

One thing worth noting: the 7.5s cycle length and 3s grow/shrink durations are hardcoded independently in both `script.js` (the `setTimeout`/`setInterval` values) and `style.css` (the `grow`/`shrink`/`rotate` keyframe durations). They currently agree, but changing one without the other would desync the text from the animation.

## How to Run

Open `index.html` in a browser. It expects `img/bg.jpg` to be present alongside it for the background.

## Technologies

CSS `conic-gradient`, CSS keyframe animations, vanilla JavaScript timers.

## What I Learned

How to sequence UI changes with nested timeouts instead of a single animation loop, how reassigning `className` (instead of toggling individual classes) can be used to force a CSS animation to restart, and how `aria-live` makes text that changes on its own actually useful to screen reader users.

## Future Improvements

- Pull the 7.5s/3s timing values into one shared source instead of duplicating them across JS and CSS
- Add a way to pause or stop the cycle
- Make the phase durations configurable instead of fixed
