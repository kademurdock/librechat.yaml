# Platform Status — shared ground truth for Forge + Cowork

This file is the shared brain between Forge (Kade-AI's own ops/dev agent) and Kade's Cowork/Claude sessions. Either one might pick up work the other started, so both read this at the start of a session and both write to it — not just at a clean end, but before starting anything multi-step and after finishing a meaningful chunk, since sessions can end abruptly (a browser tab closing, a computer crashing, a context limit) with no warning. Kade's fuller local history lives in `PROJECT_STATUS.md` on her own computer (Cowork-reachable only, not Forge); this file is the smaller, cross-reachable subset both agents actually need. Newest entries go at the top.

Why this repo: `librechat.yaml` has no auto-deploy webhook, so commits here never trigger a redeploy — safe for frequent small updates from either agent.

---

## [2026-07-18 — Cowork] Forge back to glm-5.2 + 105 agents moved to deepseek-v4-pro

**What changed (Kade's explicit ask, both halves):**
- **Forge reverted:** `moonshotai/kimi-k3` → `z-ai/glm-5.2` (provider `OpenRouter`), `model_parameters` carried forward byte-identical (`{temperature: 0.7, frequency_penalty: 0.3, presence_penalty: 0.2, promptCacheTtl: "1h", reasoning_effort: "none", top_p: 0.9, maxContextTokens: 1000000}`). Verified by fresh independent GET: model correct, 42 tools + 6 actions intact, now v59. The K3 experiment lasted one day — the July-17 "reasoning always-on, 4–6x cost" heads-up is moot now. Backup: `backups/forge_backup_pre_glm52_return_20260718.json` (Kade's local folder).
- **Marketplace fleet:** the 104 personas that were on `z-ai/glm-5.2` PLUS Muse (last Hermes 405B holdout) → `deepseek/deepseek-v4-pro` (via OpenRouter, same as Kiana & co., NOT the direct DeepSeek endpoint). 105 PATCHes, each agent's own `model_parameters` kept unchanged with `reasoning_effort` carried forward. Paced 4s/call, zero failures. Spot-verified by fresh GETs (9-agent spread + Kiana + Muse): models landed, tool counts unchanged, Forge's 42 tools re-checked after the batch per the standing rule.
- **Deliberately NOT moved (Kade delegated "best judgement based on what they can do"):** the 86-agent July-17 batch stays on `deepseek/deepseek-v4-flash` (same DeepSeek v4 family, same persona job, ~4.4x cheaper — pro buys them nothing); the 23 vision agents stay on `minimax/minimax-m3` (they need vision; OpenRouter's DeepSeek has been text-only).

**Model roster (live-verified, all 221 agents individually GET'd this session):** Forge = `z-ai/glm-5.2`. Kiana/Zadiana/Deuce/Lyric/Cadence (+1 more special) = `deepseek/deepseek-v4-pro` (unchanged, were already there). 104 personas + Muse = `deepseek/deepseek-v4-pro` (NEW). 86 new-batch personas = `deepseek/deepseek-v4-flash`. 23 vision agents = `minimax/minimax-m3`. Hermes is now fully retired from the fleet. → 111 of 221 on v4-pro.

**Also resolved from July-17 "unconfirmed":** the 104-agent batch DID complete — live count is exactly 221.

**Revert:** full pre-change records of all 221 agents: `backups/fleet_records_pre_deepseekpro_20260718.zip`; per-agent old model + `model_parameters` for the 105 switched: `backups/rollback_list_deepseekpro_20260718.json` (both in Kade's local folder). Rollback = PATCH each id back to `old_model` with `old_model_parameters`.

**Watch for:** nothing structural — v4-pro was already proven on Kiana & co. If a persona sounds off, it's tuning (their glm-era `temperature: 1` came along unchanged), not plumbing.

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