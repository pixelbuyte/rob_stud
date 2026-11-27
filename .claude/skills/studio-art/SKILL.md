---
name: studio-art
description: Use when building parts, lighting, signs, VFX or UI for any Roblox game in this repo - palette tokens, materials, fonts, motion and the banned patterns that make builds look AI-generated.
---

# Art rules

Each game defines its own palette in a `Theme` module and in its GAME.md. Game-specific art skills
(e.g. `wao-art-direction`) override the palette and mood but keep every rule below.

## Banned

- `Material.Neon` on floors, walls, platforms or whole signs. One meaningful accent at most.
- `BrickColor` names. Use `Theme` or `Color3.fromRGB`.
- Emoji in world signs or UI text.
- Gradients, drop shadows, rainbow or thick UIStroke, pulsing borders, screen shake.
- Default novelty fonts (Arcade, Cartoon, SourceSans). Use Builder Sans.
- More than one saturated colour competing in the same view.
- Free models or images you cannot trace to an approved source.

## Palette structure (every game fills these keys)

| Key | Role |
|---|---|
| BaseDark | Floors, UI panels |
| Surface | Walls, props |
| SurfaceLight | Trim, inactive elements |
| Text / TextMuted | UI text, secondary labels |
| Accent | The single UI highlight colour |
| SignalGood / SignalBad / SignalWarn | Only where they mean something |

Signal colours are muted, not saturated. Every signal also has a shape, icon or text.

## Materials

Concrete floors, Slate walls and rims, Metal machinery, SmoothPlastic zones and buttons. Add
detail only after the graybox silhouette reads well.

## UI

- One ScreenGui built in code, `IgnoreGuiInset = true`, `ResetOnSpawn = false`.
- Builder Sans Bold headings, Medium body, sentence case.
- Panels: BaseDark around 0.15 transparency, UICorner 6, one 1 px UIStroke.
- Scale-based UDim2, `UIAspectRatioConstraint` where shape matters, `UITextSizeConstraint` so
  phone text stays readable. Respect safe-area insets; keep clear of native touch controls.
- Motion: TweenService, 0.2–0.35 s, Quad Out, fade plus small slide at most.
- Shop UI follows the same rules: no flashing "BUY NOW", no fake timers.

## Lighting

Future technology, low ambient, one key light per area, subtle ColorCorrection and Atmosphere.
Lighting carries the mood, not colour saturation.

## Before you finish

Scan for Neon, BrickColor, emoji and non-Builder-Sans text. Fix them. Check the view on a phone
emulator size.
