---
name: researching-a-reference-game
description: Use when the user wants to build a game like, inspired by, or cloning an existing game ("a WoW arena clone", "like Smash Melee", "Rocket League but..."), at project start or before designing any mechanic borrowed from that game, especially from a short or vague prompt.
---

# Researching a Reference Game

## Overview

When a game is "like X", **X is the spec, and your memory of X is not.** Agents write confident, wrong rules from memory ("facing isn't checked for cast-time spells") and cite pages they never opened ("SmashWiki says roughly..."). Every such rule becomes a bug the player finds in play-testing.

Before brainstorming, research X **the way its best players know it**: the techniques experts use and the exact mechanics those techniques depend on. Use the most authoritative sources that exist for that exact version.

**REQUIRED NEXT STEP:** hand the research document to superpowers:brainstorming. This skill runs first; it does not replace the design process.

## The Process

1. **Pin the reference.** Pin down the game, the version, patch or era, and the mode: "WoW 3.3.5a, 2v2 arena", not "WoW"; "Melee NTSC 1.02, tournament rules", not "Smash". Rules differ between versions. If the prompt doesn't say, ask that one question first. Also ask whether the game might be sold one day, which decides how strict licensing must be.

2. **Find sources, best tier first.** Only use sources for the pinned version.

   | Tier | Kind | Examples |
   |---|---|---|
   | 1 | Reimplementations: emulators, decompilations, open-source servers. Their code is ground truth. | TrinityCore/AzerothCore (WoW), doldecomp/melee and Slippi (Melee) |
   | 2 | Data mines and frame data for the exact version | Melee frame data sites, client data tables |
   | 3 | Expert community: pro guides, tech glossaries, tournament analysis, theorycraft simulators | WoW arena PvP guides, Smashboards tech threads, wowsims |
   | 4 | General wikis | Wowpedia, SmashWiki |
   | 5 | Your memory | Always marked **unverified** |

   **Read code, not summaries.** Web page fetchers summarize and truncate large files, so the function you need goes missing. For tier 1:
   - `git clone --depth 1 <repo>` into a scratch folder outside the project, or `curl` the raw file (`raw.githubusercontent.com/<org>/<repo>/<branch>/<path>`);
   - then search it (grep for the function, constant or error message), read the matching lines, and cite file and line.
   - Get licenses from `curl -s https://api.github.com/repos/<org>/<repo>` (the `spdx_id` field), not from the repo web page.
   - If a source is blocked or can't be found, record that in the sources table and move to the next tier. Never fill the gap from memory.

3. **Build the expert-play inventory.** From the tier 3 sources, list the advanced techniques top players use. For each one, record the mechanic it depends on and the exact rule that mechanic needs. These techniques are the clone's acceptance tests: if one can't be done, the clone is wrong.
   - **WoW examples:** fake-casting relies on interrupts landing only during a cast, plus school lockouts. Pillar line-of-sight relies on LoS being re-checked when the cast completes. Getting behind a caster relies on facing being re-checked at completion.
   - **Melee examples:** wavedashing relies on airdodge momentum into the ground. L-canceling relies on landing lag being halved within a timing window. DI relies on the knockback angle being influenced by the stick.

4. **Write the rules reference** using `reference-template.md`. Every rule records:
   - what it says;
   - its source (link plus file and function, or page section);
   - the version it applies to;
   - **verified**, meaning you opened that source this session, or **unverified**.
   Never mark something verified that you didn't open.

5. **Fill the coverage matrix.** For each core system, make a grid of conditions against phases. For casting: range, line of sight, facing, moving, stunned and so on, against start, during, completion, each channel tick and projectile flight. For a fighter: player states against inputs. Fill every cell from the sources. An empty cell is a hidden bug; record it as an open question.

6. **Capture the rest of the picture:**
   - the original's netcode model;
   - its UI and HUD conventions;
   - the community's top complaints, which are things to fix rather than copy;
   - architecture patterns from the tier 1 code worth mirroring.

7. **Licensing and IP**, one line per source:
   - GPL or AGPL: reference only. Learn the behaviour and write your own code; never paste or translate it line by line.
   - MIT or BSD: may be ported, keeping the copyright notice.
   - The original game's data, art, audio and names: never ship them. Names are placeholders to rename before release.

8. **Start the overrides list.** Record every place the user deliberately departs from the original, approved by the user.

9. **Hand off.** Save the document to `docs/reference/<game>-research.md` and commit it. Then go to brainstorming. In later milestones, check each new mechanic against this document and its sources before designing it.

## Red Flags: stop and open a source

| Thought | Reality |
|---|---|
| "I know how X works" | That's tier 5. Open a tier 1–3 source, or mark the rule unverified. |
| "SmashWiki/Wowpedia says..." when you haven't fetched it this session | That's a citation from memory. Fetch it or mark the rule unverified. |
| "High confidence, well documented" | Confidence isn't a source. |
| A source for a different version (Ultimate data for Melee, retail for 3.3.5) | Wrong spec. Find the right version or mark the rule unverified. |
| "The basics are enough for a prototype" | Basic play hides the rules experts exploit. Those rules are the ones you'll get wrong. |
| "No reimplementation exists" (said without searching) | Search first: "<game> emulator", "decompilation", "private server", "open source remake". |
| "The page fetch didn't show the function, so the wiki will do" | Clone the repo or curl the raw file, then grep. Wikis are tier 4. |
| "This wiki page is about the game" | Check which version it describes. Wikis often mix expansions or patches. Treat an unpinned page as tier 4 at best. |
