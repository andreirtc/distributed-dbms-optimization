# Coffee Shop Website — Creative Direction and Approval Rules

Status: ideation only. CREMA & CRUMB is a provisional prototype name, not a chosen brand. Ralph (Chromebook) develops the storefront; Menard hosts/integrates it. This brief supplements AGENTS.md; the detailed master prompt and addendum from the design conversation give fuller requirements. If unavailable, ask Ralph for them. Do not treat any example name, HEX palette, product count, or technical approach as approved.

## References and first response
Inspect the original HTML, three issue screenshots and GetLayers.ai inspiration recording when supplied. Do not pretend unavailable attachments were reviewed. The HTML is a single-file concept with Canvas product cutouts, Three.js and mock browser storage; preserve useful ideas, not necessarily its implementation. The video is inspiration for flow, visual pacing and storytelling, not an instruction to copy branding or layouts. Do not publish reference videos or third-party assets in the public repo without reuse rights.

First response: Gate A ONLY. Present at least 20 distinct name options (including short, invented, Filipino-inspired and premium-modern directions); at least 10 distinct palettes with HEX values applied to comparable cup, packaging and UI examples; and visual comparison of modern accessible chain, premium artisan bakehouse, and hybrid specialty chain. Show actual visual boards/previews where possible. Shortlisting is not approval. Wait for explicit feedback before moving forward.

## Creative gates (independent of DBMS gates 0–5)
- A: name, palette and business positioning exploration.
- B: four to six full brand concepts based on shortlisted choices; approve name, colors, typography, packaging and positioning.
- C: three original desktop/mobile landing page alternatives and motion storyboard (restrained, balanced, cinematic); approve flow and shelf design.
- D: roughly 12 flagship product previews; compare clean/selective/full physical beverage realism and realistic cutouts/2.5D/3D pastry approaches. Approve before expanding the catalog.
- E: integrated interactive prototype with working navigation, product studio and ordering using clearly labeled mock data. Review desktop/mobile.
- F: consolidated design system, approved blueprint, source/asset handoff and technical acceptance. Full production implementation only after approval.

Maintain a decision log distinguishing approved, rejected, provisional and undecided. Ask for major creative decisions, not every minor implementation choice. Do not silently replace approved decisions. Show alternatives instead of asking users to choose unfamiliar terminology. If the tools cannot provide a mockup or working preview, say so and provide a practicable alternative.

## Non-negotiable visual goals
Premium modern original identity, no generic template aesthetics. Frameless product cutouts that physically rest on shelves with convincing contact shadows, perspective and proportion; shelf material may change after approval. No conventional boxed product cards. Recipe-specific organic irregularity, not merely recoloring shared noise patterns: differentiate Matcha Mango and Yuzu Honey Matcha, fruit compote, espresso diffusion, blended textures and Americano transparency. Use consistent product appearance specifications for thumbnails and 3D. Realistic geometry and texture for individual pastries, not color-swapped shapes. Beverage ice/condensation/bubbles and pastry 3D approach are intentionally undecided. Interactive drinks with drag/tilt, front/top view and appropriate recipe-based size, milk, sweetness, shots, ice and toppings. Fit camera to model bounds including whipped cream and viewport aspect ratio; both 12 oz and 16 oz must remain fully visible, with believable relative sizes. No scroll hijacking; reduced motion and keyboard access.

## Functional and implementation scope
Proposed 100–150 believable products, validated with a small flagship set first. Landing, catalog/search, customization, accounts, favorites, cart, branch availability, pickup/scheduling, simulated payment, order history/status/reorder and rewards. Optional commercial extras only after essential functions. Proposed modular React/TypeScript/Vite, Tailwind/custom CSS and Three.js if justified. No need to keep monolithic HTML. Mock API separate from UI; no direct browser access to PostgreSQL. Export maintainable source and assets for local hosting; avoid external CDN/runtime dependencies, make static thumbnails efficient, load 3D on demand and dispose of resources. No invented Lighthouse, SEO, FPS or DBMS scores. Coordinate shared API/ERD with group; no unilateral DB changes or cloud database. Database work need not wait for final branding.

## DBMS data scope (proposal only)
Products/categories, variants/options, recipes as needed, branch inventory, customers/accounts, orders/items/selected customizations, payments, inventory movements, loyalty, promotions/favorites. Use reproducible synthetic histories rather than grocery data; scale transaction tables rather than visible catalog. Document data provenance and validate relationships, totals and business rules. Shared schema, API and benchmark design require team review.
