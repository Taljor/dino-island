# Dino Island — install on the kids' iPads

A 3D "hatch → eat → grow → unlock skins" dinosaur game. Plain static files (HTML + the Three.js library, bundled); no app store, no Mac, no build step, no outside servers once installed.

## Put it on the iPad (10 minutes)
1. Host this folder on any HTTPS static host. Easiest: **GitHub Pages** — new public repo → upload these files → Settings → Pages → "Deploy from a branch" (main, / root) → the site appears at `https://<you>.github.io/<repo>/` in a minute or two. Cloudflare Pages / Netlify Drop (drag the folder) work the same way. A home-lab box works too if it has a real certificate (Caddy auto-HTTPS, or `tailscale serve`).
   HTTPS is required for the service worker, which is what makes the game work offline; plain http:// on the LAN plays fine but won't install for offline use.
2. On the iPad, open the URL in Safari **while online** once (this caches the game and the Three.js library).
3. Tap Share → **Add to Home Screen**. Launch it from the icon: full screen, landscape, works with Wi-Fi off.
4. Optional lockdown: Settings → Accessibility → **Guided Access** → on. Open the game, triple-click the top button to start a session.

Progress saves automatically (every 8 s and whenever something important happens) in the browser's storage for that home-screen app.

## Controls
- **Left stick** (bottom-left; floats to wherever the thumb lands): push up = walk forward, pull down = back up, push to the edge = run. Sideways does nothing.
- **Right stick** (bottom-right): turns the dino left/right. The camera is locked behind the dino, so it turns with it.
- **Tap the ground** (upper part of the screen) → the dino walks there. Tap water → walks to the nearest shore.
- **Drink** button appears bottom-center near water.
- Keyboard: W/S forward/back, A/D or ←/→ turn, Shift = run, Space/E = drink.

If anything goes wrong on a device, a red bar appears at the bottom of the screen with the error text. The ⚙️ menu's Parent zone shows a diagnostics line (canvas size, camera/dino positions, camera distance).

## Species and diets
Nine starting dinosaurs, chosen on the hatch screen (the nest shows a live 3D preview):
- Carnivores 🥩 (eat 🐟 fish and 🍖 snacks): Rex, Raptor (fast, feathered), Spino (loves fish, can wade deeper)
- Herbivores 🌿 (eat 🍓 berries and 🌿 leaves): Trike, Stego, Longneck (grows huge), Anky
- Omnivores 🍖🌿 (eat everything): Galli (fastest), Ovi
Each species has a favorite food that gives bonus growth, its own size and speed, and the `SPECIES` table at the top of `index.html` is plain data — add a species by giving it a body type, size, speed, diet, favorite and a list of features (frill, horns, plates, tailspikes, sail, armor, club, crest, feathers, longneck, longsnout, sickle, teeth).

## Hunting and defending (size matters, nobody dies)
- The **action button** (bottom-center, next to Drink; keyboard F) is 🦷 **Bite** for carnivores/omnivores and 🛡️ **Defend** for herbivores.
- Bite a wild dinosaur no bigger than ~1.25× your size and it's eaten in a puff of stars: big hunger refill and +4 growth. It respawns in its home biome a minute later. Anything bigger just gets bonked.
- Defend/bonk sends any nearby dinosaur (or turtle) flying with stars; it runs off for a few seconds. Carnivore NPCs (Rex, Raptor, Spino) chase and bump players smaller than themselves — a bump costs a little hunger and a short stumble, never health. Run (they're slower than a sprinting player) or bonk them back.
- Wild dinos come in sizes; the big Rex lives in the badlands and the Spino in the swamp, so hatchlings learn to steer clear until they've grown.
- Trophies: **Bonk!** (5 knockbacks) and **Top of the Food Chain** (5 hunts for meat-eaters, 10 knockbacks for herbivores) unlock the Tiger and Shadow skins.

## Camera
The camera bobs with the stride and widens slightly when running. Turn it off under ⚙️ → Camera bounce if it bothers anyone. The ⚙️ menu also has a **Field of view** slider (60°–110° horizontal; 80° default) that previews live behind the menu.

## The island (six biomes)
The island is ~2.3× bigger with the nest in the middle:
- 🌼 **Sunny Meadow** (center) — round trees, flowers, the lake; berries and leaves.
- 🌴 **Tangle Jungle** (east) — tall dark trees, ferns, The Great Tree with fireflies at night; leaves galore.
- 🐸 **Croaky Swamp** (south) — mangroves, reeds, lily pads, pools, Lily Lagoon with hopping frogs; most of the fish.
- 🌵 **Dusty Dunes** (west) — dunes, cacti, red rocks, Sunset Beach and the Hidden Oasis.
- ❄️ **Snowy Peaks** (north-west) — a high plateau, snow-capped pines, boulders, glowing ice and the Crystal Cave.
- 🌋 **Ash Badlands** (north) — dead trees, obsidian, lava pools (you can't walk into them), Rumble Volcano and the Fossil Arch.
A toast announces each biome as you enter it and the HUD shows its icon. Biome sectors, colors and plant lists are in the `BIOMES` table and the vegetation block; each landmark counts toward the Explorer trophy (7 in total).

## Egg Hunt
Six eggs are hidden around the island (never near the nest; a little sparkle gives them away up close, and the hint arrow points to the nearest one when you're not hungry or thirsty). Walk into an egg to pick it up — it rides on your back — and carry it home to the nest. It warms for 20 seconds and hatches into a baby of that species, which follows you in a parade (up to 6; extras play at the nest). A predator bump knocks the egg off your back, so Defend has a job. Trophies: **Egg Hunter** (first hatch) and **Full Nest** (six hatches) unlock the Speckled and Coral skins. Babies and the nest state are saved.

## Pixel art icons
Every emoji has been replaced by an original 16×16 pixel icon (see `icons-sheet.png`). The whole set follows one system: a single tinted-dark ink outline (`#0a1f14`, never pure black), light from the top-left, a 24-color palette of hue-shifted three-step ramps (shadows lean cool, highlights lean warm), a 1px safe border, side-view dinosaurs facing right, and flat front-on objects. Icons are stored as text grids in `PX` at the top of the script — one character per pixel, `.` for transparent, letters for palette entries in `PX_PAL` — and rendered to crisp data-URL images at boot, so there are no image files to host and nothing to license. To add or edit an icon, edit its 16 rows of 16 characters.

## Day and night
Night is **off by default** — the sun dips low and glows golden but never sets, so the island is always bright. ⚙️ → Night time turns the full day/night cycle (stars, moon, fireflies, the calm night music) back on. Days still count every 4 minutes either way.

## Music
The chiptune soundtrack is generated in code (pulse-wave lead, triangle bass, arpeggios, noise drums) — original, no files, no licensing. A bouncy day theme and a calm night theme swap at the bar line with the in-game clock. Toggle it under ⚙️ → Music; sound effects have their own switch. Notes live in the `Music.themes` table (MIDI numbers, 16 steps per bar, -1 holds a note) if you want to write your own tune.

## Tune the game (top of `index.html`, `CONFIG`)
- `stageThresholds: [12, 30, 55]` — growth points needed for Juvenile / Sub-Adult / Adult. Lower = faster growth.
- `hungerDrainPerSec`, `thirstDrainPerSec` — how quickly the bars empty (there is no death; low bars only slow growth and speed).
- `dayLengthSec: 240` — real seconds per in-game day.
- `food.*.count / respawn / growth` — how much food is on the island and what it is worth.
- `SKINS`, `ACHIEVEMENTS`, `LANDMARKS` — plain data; add a skin (4 colors) and an achievement that unlocks it.

## Shipping an update
Edit `index.html`, then change `CACHE = 'dino-island-v1'` in `sw.js` to `v2`. The iPad picks up the new version the next time it opens the game while online.

## Next steps (from the build plan)
- Swap the procedural dinosaur for a rigged GLB (Quaternius CC0 pack or a Tripo/Meshy auto-rigged model) via `GLTFLoader`; keep `growth → scale` and the stat curves as they are.
- Add species as new entries in the skin/rig tables; add nesting/eggs as a v2 feature.
- If the web version ever feels limiting, the same design ports to Godot 4 (Mobile renderer) — the data tables and state machine translate directly.
