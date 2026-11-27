---
name: wao-art-direction
description: Use whenever building parts, lighting, signs or UI for Wrong Answer Only - sets the palette, materials, fonts and banned patterns so the game doesn't look AI-generated.
---

# Wrong Answer Only - art direction

The look is a dark, matte game-show stage. Near-black and slate surfaces, one blue accent
for interface, colour reserved for things that mean something.

## Banned (these made earlier builds look vibe-coded)

- `Material.Neon` on floors, platforms, walls or signs. Only allowed: the Map_Platform
  lava floor and a single thin accent strip if a step asks for it.
- `BrickColor` names ("Bright red", "Lime green"). Use `Color3.fromRGB` or `Theme`.
- Emoji in world signs or UI text.
- Gradients, drop shadows, rainbow or thick UIStroke, pulsing borders, screen shake.
- Default Roblox fonts (Arcade, Cartoon, SourceSans). Lightning flashes, rain, fog walls.
- More than one saturated colour competing in the same view.

## Palette (Theme module)

| Key | Color3.fromRGB | Use |
|---|---|---|
| BaseDark | 14, 16, 20 | Lobby floor, UI panels |
| Surface | 32, 36, 44 | Walls, pedestals, corridors |
| SurfaceLight | 58, 64, 76 | Trim, edges, inactive platforms |
| Text | 232, 236, 242 | UI text |
| TextMuted | 140, 148, 160 | Secondary labels |
| Accent | 47, 111, 237 | UI highlights, progress, selection, BlueZone |
| SignalGreen | 76, 160, 104 | SAFE platform, "survived" |
| SignalRed | 200, 64, 58 | Danger, elimination, lava |
| SignalAmber | 214, 160, 54 | Coins, warnings, correct-button reveal |

## Materials

Concrete for floors, Slate for walls and rims, Metal for pedestals, arches and coins,
SmoothPlastic for zones and buttons.

## Type

`Enum.Font.BuilderSansBold` for headings, `Enum.Font.BuilderSansMedium` for body.
Rule cards: round number small in TextMuted, rule text large in Text. Sentence case.

## UI

- One ScreenGui built in code, `IgnoreGuiInset = true`, `ResetOnSpawn = false`.
- Panels BaseDark at 0.15 transparency, `UICorner` 6, one 1 px UIStroke in Surface.
- Scale-based UDim2 sizes, `UIAspectRatioConstraint` where shape matters, `UITextSizeConstraint`
  so text stays readable on phones.
- Motion: TweenService only, 0.2-0.35 s, Quad Out. Fade plus an 8 px slide at most.

## Lighting baseline

Technology Future, ClockTime 18.5, Brightness 1.5, Ambient (20, 22, 28), OutdoorAmbient
(40, 44, 54), EnvironmentDiffuseScale 0.4, ColorCorrectionEffect Contrast 0.1 Saturation
-0.15, Atmosphere Density 0.3. One key light per map; interiors use dim SpotLights.

## Before you finish

Scan what you built for any Neon, BrickColor, emoji or non-Builder-Sans text and fix it.
