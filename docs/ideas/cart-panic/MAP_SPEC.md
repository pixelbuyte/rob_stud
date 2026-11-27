> **Parked reference, not active instructions.** Kept so the idea is not lost. Do not build from this until the owner makes this game active in `CLAUDE.md`. Studio-wide rules in `/CLAUDE.md`, `docs/DESIGN_RULES.md` and `docs/MONETIZATION.md` win over anything here.

# Map 01 — Mega Mart after closing

## Floor plan (not to scale)
```
                      NORTH / DEEP STORE (-Z)
  +---------------------+---------------+---------------------+
  | FREEZER (medium)    | MANAGER VAULT | STOCKROOM (high)    |
  | cold cyan / vents   | optional risk | red / conveyor      |
  | shelf loops         |               | shelf loops         |
  |                     | MAIN AISLE    |                     |
  +.....................+...............+.....................+
  |                  CROSS AISLE / RETURN ROUTE               |
  +.....................+...............+.....................+
  | PRODUCE (low)       | MAIN AISLE    | ELECTRONICS (med.)  |
  | warm yellow         |               | purple / bulky loot |
  | shelf loops         |               | shelf loops         |
  +---------------------+---------------+---------------------+
  |                   CHECKOUT / CART SPAWN                   |
  +------------------------ EXIT -----------------------------+
                          LOBBY (+Z)
```
## Geometry (studs)
Interior x=-96..96, z=-72..72, ground y=0. +Z is front. Main aisle 22 studs wide. Cross aisle approximately 14 studs wide. Nominal shelf width 4; shelf row center spacing 20; shelf segments 16 long with 8-stud end gaps. Prototype cart width 6 studs, so test 8-stud gaps and turns carefully; increase clearance if necessary. Vault front opening nominal 12 studs. Main front opening 24 studs. Store has no roof to ease editing; production art can add overhead structure without hiding the camera. Lobby spans x±40 and z72..106.

| Zone | Approx. center (x,z) | Risk | Visual identity |
|---|---|---|---|
| Lobby | 0,89 | safe | bright storefront, carts |
| Checkout | 0,63 | safe | illuminated exit arrows |
| Produce | -49,35 | low | yellow, oversized fruit |
| Electronics | 49,35 | medium | purple, colorful screens |
| Freezer | -49,-35 | medium | cyan, cold vapor |
| Stockroom | 49,-35 | high | red, metal crates |
| Vault | 0,-62 | high | gold accents, security door |

## Navigation and pacing
Spawn in lobby, get into round near checkout. Safe produce is obvious from entry. Main spine and cross aisle keep return route readable; perimeter and shelf aisles allow alternative loops. Aim for <15s to reach first loot and roughly 20–35s from deep zones to exit depending on speed/route; measure with the actual cart, not just player sprint. Vault should be a choice, not a mandatory bottleneck. No hazard may permanently block the only exit. Provide a fallback if the cart is stuck. Keep camera occlusion, touch input, and color-blind-readable symbols in mind. Avoid hidden hazards near spawn.

## Anchors
`CollectionService` tags: CP_LootSpawn (26), CP_CartSpawn, CP_Extraction, CP_RoundEntry, CP_HazardAnchor (5), CP_GateAnchor. All markers have MarkerId, MarkerType, Zone, RiskTier attributes. Anchors are not gameplay triggers. Marker positions are design candidates. Extraction marker is centered at z69, not the full trigger volume; server builds/validates a suitable extraction zone. Vault marker is not a functional gate. Markers may be hidden in production. Generated root has ownership attribute and layout version.

## Art pass after gameplay approval
Toy-like chunky silhouettes, clean color-coded departments, oversized recognizable loot, fluorescent signage, readable countdown. Unique cartoon cleaning robot. Use original or licensed assets, track provenance, and audit free models for scripts. No real brand logos. Prioritize lights and landmarks over decoration; optimize meshes and textures, keep essential UI legible on small screens. Test with low graphics settings.
