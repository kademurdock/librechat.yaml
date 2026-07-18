# Platform Status — shared ground truth for Forge + Cowork

This file is the shared brain between Forge (Kade-AI's own ops/dev agent) and Kade's Cowork/Claude sessions. Either one might pick up work the other started, so both read this at the start of a session and both write to it — not just at a clean end, but before starting anything multi-step and after finishing a meaningful chunk, since sessions can end abruptly (a browser tab closing, a computer crashing, a context limit) with no warning. Kade's fuller local history lives in `PROJECT_STATUS.md` on her own computer (Cowork-reachable only, not Forge); this file is the smaller, cross-reachable subset both agents actually need. Newest entries go at the top.

Why this repo: `librechat.yaml` has no auto-deploy webhook, so commits here never trigger a redeploy — safe for frequent small updates from either agent.

---

## [2026-07-17, session 5 — Cowork] Forge switched to Kimi K3 + this file created

**What changed:**
- Forge's model: `z-ai/glm-5.2` → `moonshotai/kimi-k3` (provider stays `OpenRouter`). Kade's call, and a real switch, not a pilot — Forge's actual job (coding, tool use, multi-step ops work) is exactly where Kimi K3 benchmarks well, unlike the roleplay/companion fleet where it doesn't make sense.
- `model_parameters` carried forward unchanged: `{temperature: 0.7, frequency_penalty: 0.3, presence_penalty: 0.2, promptCacheTtl: "1h", reasoning_effort: "none", top_p: 0.9, maxContextTokens: 1000000}`.
- **Heads up for whoever reads this next:** Kimi K3 ships with reasoning always-on, locked to maximum — it can't actually be turned off yet. Forge's `reasoning_effort: "none"` is now inert (harmless, just doesn't do anything anymore). Expect Forge to respond slower and more verbosely than the old glm-5.2 setup, and to cost more per message (K3 runs roughly 4x glm-5.2's input price, 6x output). That's expected, not a bug — don't "fix" it by itself.
- Verified live via a fresh independent GET after the PATCH: model, tools (42), and actions (6) all confirmed correct and untouched.
- **Revert:** PATCH `model` back to `z-ai/glm-5.2` with the `model_parameters` block above (or straight from the backup) — full pre-switch agent record is saved at `backups/forge_pre_kimik3_switch_20260717.json` in Kade's local folder.
- **Watch for:** tool-calling reliability on Kimi K3 through whatever OpenRouter provider serves it. K3 is days old, and this platform has already hit "provider silently drops the tools param" once before (the Hermes/Novita saga). If any of Forge's tools misbehave, that's the first thing to check — may need a pinned/ignored provider, same fix pattern as last time.

**This file itself:** created this session because Kade wants Forge and Cowork sessions to be able to pick up each other's work — "he may someday have your job and you may someday have his." Forge reaches it via his existing `readGitHubFile`/`commitToGitHub` tools (already live, no new tools needed). Cowork reaches it via the same GitHub PAT it already has for everything else. A standing instruction was added to Forge (July 17 update block) telling him to read this at session start and write to it at natural checkpoints, not just at a clean end. Cowork sessions should do the same going forward — check here in addition to (not instead of) `NEXT_SESSION_PASTE.md`, which stays as Cowork's own local quick-start.

## Ground truth as of 2026-07-17 (session 4, carried from NEXT_SESSION_PASTE.md — reverify, don't trust blindly)

fork `675c0ae` (on `80e599c`) · inworld `82bb3b3` (on `47f0f50` on `1f4f478`) · bridge `af623e0` · reframe `c58e4e8` · librechat.yaml `f4bf372` · kade-ai-app `dee12d1` (on `a79c9ef`, Codemagic build `6a5a3d33` GREEN → TestFlight).

**Model roster (as of this write):** Forge = `moonshotai/kimi-k3` (NEW, this session). Kiana/Zadiana/Deuce + the audio trio (Lyric/Cadence/Muse) = Hermes 4 405B. ~86 other marketplace personas = Hermes 4 70B. 24 vision agents = minimax-m3 (Hermes and Kimi both do vision, but this roster hasn't been revisited since). New agent-creation template (the 104-agent batch) = `deepseek/deepseek-v4-flash`.

**Known in-flight / unconfirmed — verify before assuming either way:** the 104-agent marketplace batch's actual completion state as of this write is NOT confirmed in this file. The last record (`FORGE_HANDOFF_AGENT_BATCH_2026-07-17.md`, Kade's local folder) says zero agents were created yet and Forge was picking it up mid-session. Whoever reads this next should check a live agent count against the ~221 target before assuming it's done, still pending, or partially done.

---

*(Older entries go below as this file grows. If it gets unwieldy, the same weekly-consolidation instinct already used for memory cards applies here too — summarize and archive old entries, don't just let this run forever.)*
