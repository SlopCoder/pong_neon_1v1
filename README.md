# Neon Pong

A browser-based implementation of the classic Pong arcade game, built entirely with vanilla HTML5 Canvas and JavaScript. The game features a synthwave-inspired neon aesthetic, two gameplay modes, three bot difficulty levels with distinct AI strategies, synthesized sound effects, and a full menu system with mouse and keyboard support.

No external libraries, frameworks, or assets are required. The entire game runs from a single HTML file.

---

## Table of Contents

1. [Overview](#overview)
2. [Features](#features)
3. [Getting Started](#getting-started)
4. [How to Play](#how-to-play)
5. [Game Modes](#game-modes)
6. [Bot Difficulty Levels](#bot-difficulty-levels)
7. [Controls Reference](#controls-reference)
8. [Game Mechanics](#game-mechanics)
9. [Audio System](#audio-system)
10. [Visual Effects](#visual-effects)
11. [Architecture and Technical Details](#architecture-and-technical-details)
12. [Configuration and Customization](#configuration-and-customization)
13. [Browser Compatibility](#browser-compatibility)
14. [Known Limitations](#known-limitations)
15. [License](#license)

---

## Overview

Neon Pong is a faithful recreation of the 1972 Atari Pong concept, modernized with a dark synthwave visual style and layered game-feel polish. It supports both local two-player matches (shared keyboard) and single-player games against an AI opponent. The AI uses three fundamentally different strategies depending on the selected difficulty, making each level feel like a distinct opponent rather than a speed-adjusted version of the same one.

The game is entirely self-contained. It uses the HTML5 Canvas API for rendering, the Web Audio API for procedurally generated sound effects, and requestAnimationFrame for a smooth, frame-rate-independent game loop. There are no image assets, no audio files, and no network requests.

---

## Features

- Two game modes: Player vs Player (local) and Player vs Bot.
- Three bot difficulty levels, each using a different AI strategy.
- Procedurally generated synthesizer sound effects for every game event.
- Particle effects on paddle hits, wall bounces, and scoring.
- Screen shake on goals for added impact.
- Animated countdown before each serve with per-tick audio cues.
- Score pulse animations with color-coded feedback.
- Ball trail rendering with progressive opacity.
- Serve flash effect on ball spawn.
- Pulsing "MATCH POINT" indicator at game point.
- Live rally counter during play (appears after 3 consecutive hits).
- Longest rally statistic tracked per match, shown on pause and game-over screens.
- Hover-highlighted menu options with neon glow and directional cursors.
- Clickable menus with full keyboard shortcut support.
- Contextual Pause button that appears only during active gameplay.
- Mute toggle for all audio.
- Dark theme that extends to the full viewport background.
- Responsive canvas that scales to fit the container width while maintaining aspect ratio.
- Sub-stepped ball physics to prevent tunneling at high speeds.
- Frame-rate-independent delta-time game loop.

---

## Getting Started

### Requirements

Any modern web browser with HTML5 Canvas and Web Audio API support. No build tools, package managers, or server infrastructure are needed.

### Running the Game

1. Save the HTML file to your local machine.
2. Open it in a web browser by double-clicking the file or using `File > Open`.
3. Click the canvas area to give it keyboard focus.
4. Select a game mode from the main menu.

Alternatively, the file can be served from any static web server, embedded in an iframe, or hosted on any static hosting platform.

---

## How to Play

The objective is to be the first player to reach 10 points. A point is scored when the ball passes behind the opposing player's paddle. After each point, the ball is re-served from the center of the court following a 3-second countdown.

The ball's bounce angle is determined by where it strikes the paddle. Hitting the ball with the center of the paddle produces a shallow return, while hitting it with the edge of the paddle produces a steep angle. This is the primary skill mechanic and the key to beating higher-difficulty bots.

Ball speed increases by 6% with every paddle hit during a rally, up to a defined maximum. This creates natural tension as longer rallies become progressively harder to sustain.

---

## Game Modes

### Player vs Bot

A single player controls the right (cyan) paddle and plays against an AI-controlled left (magenta) paddle. After selecting this mode, a difficulty selection screen is presented. The player can use either W/S or the arrow keys to move.

### Player vs Player (Local)

Two players share one keyboard. Player 1 controls the left (magenta) paddle using W and S. Player 2 controls the right (cyan) paddle using the Up and Down arrow keys.

---

## Bot Difficulty Levels

Each difficulty level implements a fundamentally different AI strategy, not merely a speed adjustment. This design ensures that each level presents a qualitatively different challenge.

### Easy

The easy bot tracks the ball's current vertical position in real time. It does not predict where the ball will land. Its paddle speed is capped at 300 pixels per second (compared to the player's 540), which means it physically cannot keep up with steeply angled shots. The strategy to beat it is straightforward: hit the ball off the edge of your paddle to create steep angles that outrun its tracking speed.

### Medium

The medium bot predicts where the ball will arrive at its paddle line, but it only simulates a single wall bounce in its calculation. This means any shot that bounces off two or more walls before reaching the bot will land in a position the bot did not anticipate. It also applies a random error offset of up to 50 pixels to its predicted target on each new incoming shot. Its paddle speed is 470 pixels per second. The strategy to beat it is to use multi-bounce ricochet shots by hitting the ball at steep angles near the top or bottom of the court.

### Hard

The hard bot performs a full multi-bounce prediction that simulates up to 20 wall reflections. When a new shot is incoming, it commits to an initial prediction with up to 60 pixels of random error. It then gradually refines that prediction as the ball approaches, blending its current target with the true prediction at a rate of 8% per frame. Its paddle speed is 520 pixels per second, which is slightly below the player's maximum of 540. The strategy to beat it is to use extreme paddle-edge angles and keep rallies short, because the longer a rally continues, the more time the bot has to correct its aim. Outpacing it on raw speed is possible but requires precise positioning.

---

## Controls Reference

### Keyboard

| Action | Key |
|---|---|
| Player 1 / Bot paddle up | W |
| Player 1 / Bot paddle down | S |
| Player 2 / You paddle up | Up Arrow |
| Player 2 / You paddle down | Down Arrow |
| Pause / Resume | Space |
| Select "1 player vs bot" | 1 (on menu screen) |
| Select "2 players local" | 2 (on menu screen) |
| Select Easy difficulty | 1 (on difficulty screen) |
| Select Medium difficulty | 2 (on difficulty screen) |
| Select Hard difficulty | 3 (on difficulty screen) |
| Go back from difficulty screen | Escape or Backspace |
| Rematch (game over screen) | R |
| Return to main menu (game over) | M |

### Mouse

All menu options, difficulty selections, and game-over actions are clickable. Options highlight with a neon glow effect on hover.

### External Buttons

Three buttons appear below the canvas. The Pause button is hidden on menu screens and appears only during active gameplay (countdown, playing, paused, and game-over states). The Menu button returns to the main menu from any state. The Sound button toggles all audio on or off.

---

## Game Mechanics

### Ball Physics

The ball starts each serve at a base speed of 400 pixels per second, launched from the center at a small random vertical angle. Speed increases by 6% on every paddle hit, capped at a maximum of 880 pixels per second. The ball bounces off the top and bottom walls with perfect reflection.

### Paddle-Ball Interaction

The bounce angle is determined by the hit position on the paddle. A centered hit produces a nearly horizontal return. Hitting nearer to the paddle edge produces angles up to approximately 51 degrees (pi / 3.5 radians). This mechanic is the foundation of the game's skill expression.

### Sub-Stepping

At high ball speeds, a single-step position update per frame could cause the ball to pass through a paddle entirely (tunneling). To prevent this, the physics update subdivides each frame into smaller steps, ensuring the ball never moves more than 6 pixels in a single sub-step. This guarantees reliable collision detection at all speeds.

### Scoring

The first player to reach 10 points wins the match. When either player reaches 9 points and the scores are not tied, a pulsing "MATCH POINT" indicator appears at the top center of the court.

### Serve Countdown

After every scored point (and at the start of the match), a 3-2-1 countdown is displayed before the ball is served. Each countdown number is accompanied by an audio tick, and a rising two-note tone plays when the ball is released.

---

## Audio System

All sound effects are generated procedurally at runtime using the Web Audio API. No audio files are loaded or required.

| Event | Description |
|---|---|
| Paddle hit | Two-tone square and sine wave thwack. Pitch scales with hit distance from paddle center. |
| Wall bounce | Short triangle wave tap at 180 Hz. |
| Score | Three-note descending sine tone, creating a "deflating" feel. |
| Countdown tick | Brief 880 Hz sine blip on each number. |
| Serve ("Go") | Rising two-note sine tone (880 Hz to 1320 Hz). |
| Match win | Four-note ascending fanfare (C5, E5, G5, C6). |
| Menu click | High-frequency snap using layered sine and square waves. |

Audio is initialized on first user interaction (click or keypress) to comply with browser autoplay policies. The mute button toggles all audio output.

---

## Visual Effects

### Particle System

Colored particles burst from the point of contact on every paddle hit (12 particles, colored to match the paddle) and on every wall bounce (4 white particles). When a point is scored, a large burst of 30 particles emanates from the scoring edge in the color of the scoring player's paddle.

Particles have randomized velocities, sizes, and lifetimes. They decelerate over time (velocity multiplied by 0.94 each frame) and fade out as their remaining lifetime approaches zero.

### Screen Shake

When a point is scored, the entire canvas rendering is offset by a random displacement of up to 14 pixels, which decays linearly over approximately 0.4 seconds. Paddle hits produce a smaller shake of 5 pixels.

### Score Pulse

When a player scores, their score digit briefly scales up to 1.5x its normal size and flashes in their paddle color before returning to white.

### Ball Trail

The ball leaves a trail of up to 14 past positions, rendered as progressively smaller and more transparent circles behind the current ball position. This creates a comet-like tail effect.

### Serve Flash

When the ball spawns after a countdown, a white circle expands outward from its position and fades, drawing attention to the serve.

### Title Animation

The "PONG" title on the main menu screen has a breathing glow effect (shadow blur oscillates between 22 and 40 pixels) and a subtle vertical bob of 3 pixels at 0.9-second intervals.

### Hover States

Menu options respond to mouse hover with a neon glow in their associated color and directional cursor characters that bracket the text.

---

## Architecture and Technical Details

### File Structure

The entire application is a single HTML file containing inline CSS and JavaScript. There are no external dependencies.

### Rendering

All graphics are drawn to an 800x500 pixel HTML5 Canvas element using the 2D rendering context. The canvas scales responsively within its container using CSS (`width: 100%; max-width: 680px; aspect-ratio: 8/5`), while the internal coordinate system remains fixed at 800x500 for consistent gameplay regardless of display size.

### Game Loop

The game uses `requestAnimationFrame` for its main loop. Delta time is calculated between frames and capped at 50 milliseconds to prevent large jumps during tab switches or performance drops. All movement, animation, and physics are multiplied by delta time to ensure frame-rate-independent behavior.

### State Machine

The game uses a simple string-based state machine with the following states:

- `menu` -- Main menu screen with mode selection.
- `difficulty` -- Bot difficulty selection screen (only reached via the bot mode path).
- `countdown` -- Pre-serve countdown (3, 2, 1). Paddles can move but the ball is stationary at center.
- `playing` -- Active gameplay. Ball moves, collisions are processed, bot AI runs.
- `paused` -- Gameplay frozen. Overlay shows current and longest rally stats.
- `gameover` -- Match complete. Shows winner, final score, and longest rally. Offers rematch or menu return.

### Input Handling

Keyboard input is tracked via a `keys` object that records the down/up state of each key. This allows smooth, continuous paddle movement rather than discrete steps. Mouse position is tracked relative to the canvas for hover detection in menus.

Canvas click coordinates are converted from screen space to canvas space using the canvas bounding rect and the internal-to-display size ratio, ensuring accurate hit detection regardless of CSS scaling.

### Collision Detection

Paddle collisions use axis-aligned bounding box (AABB) checks between the ball's bounding circle and the paddle rectangles. The ball's direction of travel is checked to prevent double-collision registration. On hit, the ball is repositioned to the paddle surface before the new velocity is applied.

---

## Configuration and Customization

The following constants at the top of the script can be adjusted to modify gameplay:

| Constant | Default | Description |
|---|---|---|
| `WIN_SCORE` | 10 | Points required to win a match. |
| `baseBallSpeed` | 400 | Ball speed at the start of each serve (pixels/second). |
| `maxBallSpeed` | 880 | Maximum ball speed after repeated hits. |
| `paddleSpeed` | 540 | Player paddle movement speed (pixels/second). |
| `paddleH` | 88 | Paddle height in pixels. |
| `paddleW` | 12 | Paddle width in pixels. |

Bot behavior can be tuned within the `updateBot` function by modifying the `speed`, error range, and prediction parameters for each difficulty branch.

Colors are defined in the `C` object and can be replaced to change the visual theme.

---

## Browser Compatibility

The game requires support for the following web platform features:

- HTML5 Canvas 2D rendering context
- `requestAnimationFrame`
- Web Audio API (`AudioContext`, `OscillatorNode`, `GainNode`)
- CSS `aspect-ratio` property
- ES6 JavaScript (arrow functions, `const`/`let`, template literals)

This covers all current versions of Chrome, Firefox, Safari, and Edge. Mobile browsers are supported for viewing, though gameplay requires a physical keyboard.

---

## Known Limitations

- No touch or gamepad input support. The game requires a keyboard for paddle control.
- No networked multiplayer. The two-player mode is local only (shared keyboard).
- No persistent state. Rally statistics, scores, and settings are not saved between sessions.
- Audio may not initialize until the user interacts with the page, due to browser autoplay policies. This is handled by deferring AudioContext creation to the first click or keypress.
- At very low frame rates (below 15 FPS), the delta-time cap of 50 milliseconds may cause the ball to move in visibly large steps, though sub-stepping prevents collision errors.

---

## License

This project is provided as-is for personal and educational use. No formal license is attached. You are free to modify, redistribute, and build upon this code.
