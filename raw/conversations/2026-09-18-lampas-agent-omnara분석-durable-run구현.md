# Lampas Agent 고도화

Tool: claude
Started: 2026-09-18T08:23:18.857Z
Working directory: /Users/progdesigner/Works/lampas/lampas-system
Source: /Users/progdesigner/.claude/projects/-Users-progdesigner-Works-lampas-lampas-system/81e5186a-9adc-4ea3-ae60-2d50fc174ca0.jsonl

## user

코드 최신화해줘

## assistant

원격 저장소 상태를 확인하고 최신 코드를 가져오겠습니다.

## assistant

원격에 12개 커밋이 있고 로컬에 커밋 안 된 변경이 많습니다. 충돌 여부를 먼저 확인합니다.

## assistant

겹치는 변경은 문서·모듈 등록·lock 파일 정도라 stash → fast-forward → pop 으로 진행합니다.

## assistant

충돌 4개를 확인합니다. 내용을 봅니다.

## assistant

Now port the local Tailscale doc updates from the deleted `lampas-agent-clips/CLAUDE.md` into the new `lampas-agent/CLAUDE.md`, then remove the stale file.

## assistant

omnara 와 lampas-agent 두 쪽을 병렬로 분석시키겠습니다.

## assistant

분석이 돌아가는 동안, 병합으로 바뀐 데몬 라벨·배포 대상을 메모리에 반영해 둡니다.

## assistant

두 분석 에이전트가 끝나기를 기다립니다.

## assistant

코드 최신화는 끝났고, omnara·lampas-agent 분석 에이전트 2개가 아직 실행 중입니다. 결과가 오면 개선안을 정리하겠습니다.

**최신화 결과**

- 원격 main 의 12개 커밋을 fast-forward 로 가져왔습니다. 핵심은 `lampas-agent-clips` + `lampas-agent-pulse` → `apps/lampas-agent` 병합입니다.
- 로컬 미커밋 변경 119개 파일은 stash → pop 으로 그대로 보존했습니다.
- 충돌 4건을 해결했습니다. 루트 `CLAUDE.md` 는 로컬 내용(trends 모듈·scenario 보이스 제거)을 유지하고 원격의 `lampas-agent` 이름 변경만 반영했습니다. 에이전트 `LOG.md` 는 09-17 병합 기록과 09-15 Tailscale 기록을 둘 다 남겼습니다. `setup-tailscale.sh` 는 원격의 `AGENT_*` 변수명 위에 로컬의 443 기본값과 Funnel 주석을 합쳤습니다. 원격에서 삭제된 구 `lampas-agent-clips/CLAUDE.md` 의 로컬 수정분(접속 URL·OAuth/S3 CORS 전제)은 새 `apps/lampas-agent/CLAUDE.md` 로 옮겼습니다.
- `pnpm install` 후 `apps/lampas-agent` 테스트 30개 파일 260개 모두 통과했습니다.
- 구 `apps/lampas-agent-clips`·`lampas-agent-pulse` 에는 비추적 `dist/logs/node_modules` 잔재만 남아 있어 삭제해도 됩니다. launchd 라벨이 `io.lampas.agent.daemon` 으로 바뀌었으니 맥미니 데몬은 `./scripts/deploy-agent.sh lampas-agent` 로 한 번 재설치가 필요합니다.

## user

<task-notification>
<task-id>abaf0e5b2f3c68861</task-id>
<tool-use-id>toolu_015xD3ZAsQMF5T4qizCWoS4P</tool-use-id>
<output-file>/private/tmp/claude-501/-Users-progdesigner-Works-lampas-lampas-system/81e5186a-9adc-4ea3-ae60-2d50fc174ca0/tasks/abaf0e5b2f3c68861.output</output-file>
<status>completed</status>
<summary>Agent "Analyze lampas-agent architecture" finished</summary>
<note>A task-notification fires each time this agent stops with no live background children of its own. The user can send it another message and resume it, so the same task-id may notify more than once.</note>
<result>## 1. Server — `src/server/serve.ts`, `serveLib.ts`

`serveAgent()` (`/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-agent/src/server/serve.ts:100`) is one `http.Server` doing four jobs: `/healthz`, `/v1/*` → `AGENT_PROXY_TARGET` (local lampas-api 3133), `/bank/v1/*` → `AGENT_BANK_PROXY_TARGET` (api.lampas.io, prefix stripped), everything else → `staticHandler(dist/web)` or `viteHandler()` in `--dev`. Routing is the pure `classifyRequest()`/`resolveStaticPath()` in `serveLib.ts`.

`proxy()` (serve.ts:37) is a hand-rolled pipe: no timeout, no retry, no body-size limit, no upstream health check, forwards every client header with only `host` rewritten, and 502s with the raw upstream error text.

WS: `WebSocketServer({ noServer: true })` upgraded only on `/pulse/ws` (serve.ts:138-147) so Vite HMR coexists. **There is no authentication or origin check on the WS or on any route**, while `scripts/setup-tailscale.sh --funnel` is documented as the current production exposure ("URL만 알면 누구나"). Any WS client can drive a Chrome that holds logged-in Instagram/X/Threads sessions.

Process supervision is entirely external: `launchd/io.lampas.agent.daemon.plist` with `KeepAlive` + `ThrottleInterval 10`. Logging is `console.log` into `logs/daemon.{out,err}.log` — no levels, no structure, no rotation, no request log, no correlation with `missionId`.

## 2. Pulse engine

Entry `src/server/cli.ts` → `runPulseCommand()` in `src/server/pulse/commands.ts` (`runCommand`, `watchCommand`, `loginCommand`, `listCommand`, `pushCommand`).

`runCommand()` (commands.ts:79): build `ResearchMission` from flags → `store.createMission()` → `launchBrowser()` (playwright-core, system Chrome, `browser/launch.ts`) → per platform a fresh `createResearchContext()` with `data/sessions/&lt;p&gt;.json` storage state → `runPlatformAgent()` → `selectTop()` → `saveSelections()` → `finishMission('DONE')`.

`runPlatformAgent()` (`agent/runner.ts:59`) is the "Agent Loop": phase 1 scrolls the search page calling `adapter.collectCandidateIds()` until `collectGoal` or 3 stagnant rounds; phase 2 walks candidates sequentially, `page.goto(detailUrl)` → `adapter.extractDetail()` → qualification (`effectiveViews &gt;= minViews`, age limit) → `store.upsertContent()`. **OBSERVE/DECIDE/ACT/VERIFY/EXTRACT are only log labels** — there is no decision model, no tool-calling, no replanning. Grep confirms **zero LLM calls anywhere in `src/server`**; `PulseSession.observe()` (deck/session.ts:143, commented "LLM의 눈") is dead code with no caller.

Adapters (`agent/platforms/{youtube,instagram,x,threads}.ts`, registry in `agent/registry.ts`) are regex/DOM scrapers. Instagram/Threads parse raw page HTML with regexes like `/"play_count":(\d+)/`; X reads `aria-label`s. Scoring is the closed-form `scoreContent()` in `select.ts` (log10 views + view/follower boost + optional recency).

Storage: `store/db.ts` `PulseStore` (MySQL, `migrate()` runs `CREATE TABLE IF NOT EXISTS` plus `ALTER`s wrapped in `.catch(() =&gt; {})`) or `store/remote.ts` `RemotePulseStore` (HTTP `/v1/pulse/ingest/*` with `x-pulse-token`). Both behind `PulseStoreLike` (`store/types.ts`).

Error handling: a per-candidate `try/catch` that logs and moves on; a per-platform `catch` in commands.ts:145; `finishMission('FAILED')` on outer throw. **No retries, no backoff, no 429/captcha detection.** Rate limiting is `politeDelay()` (700–1500 ms) plus fixed `waitForTimeout`s. Dedup is in-run `candidateSet` + `UNIQUE(platform, content_id)`; already-seen content is still re-fetched every cycle. Resumability: none — no candidate queue persisted, selections written only at the end, so a crash leaves a `RUNNING` mission row forever and loses selection work. Concurrency: one platform, one candidate, one browser at a time; `watch` runs `runCommand` serially.

Progress reporting: `src/shared/pulseProtocol.ts` — `mission:log`, `mission:state`, `mission:result`, `frame`, `page`, `status`. Note `PulseSession.runMission()` recovers the mission id by **regexing its own log string** (`/^미션 #(\d+) 시작/`, session.ts:203).

## 3. Control Deck

`PulseSession` (`pulse/deck/session.ts`) owns one `Browser`, one default context/page, one `Screencaster`, one `AbortController`. It is a **singleton shared by every WS client**, and `broadcast()` in serve.ts sends every frame/log to all of them — no per-user sessions, no input arbitration. `Screencaster` (`deck/screencast.ts`) streams base64 JPEG q60 at `everyNthFrame: 1` with no throttle or backpressure. Users can `goto`, click, wheel, type, and press "collect now" (`buildCurrentPageMission()`/`resolveCollectEnv()` in `deck/tools.ts`), which runs `runMissionInBrowser()` (`deck/mission-run.ts`) — the in-place path reuses the user's page for discovery and a hidden tab for detail. UI: `src/web/components/organisms/pulse/ControlDeck.tsx` + `molecules/pulse/{DeckScreen,DeckTimeline,CollectPanel}.tsx`, reducer in `src/web/lib/pulseDeck.ts`.

## 4. Clips pipeline

All orchestration runs **in the browser tab**: `runJobPipeline()` in `/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-agent/src/web/lib/runIngestPipeline.ts:437`, stages `download → probe → split → extract → transcribe → label → wiki → upload` via the `stage()` helper (line 174). Download = local lampas-api job polling (`resolveInput`, `clipsBankApi.createYoutubeJob/getYoutubeJob/fetchYoutubeFile`); split = in-browser frame diff (`sceneDetect.ts`); transcribe = `transcribe.ts` (`xai/stt-v1`, fallback `bytedance/seed-asr-2.0`) through `bankApi` → production gateway; label = `labelBatch.ts` (`google/gemini-3.5-flash`, prompt built in `buildLabelBatchPrompt()`); wiki = `runWikiStage()`; upload = `uploadLabeledClips()`.

Queue: `IngestFlow.tsx` — a single active job (`activeId`), `PipelineRunner` runs it, jobs persisted through `IngestJobStore` (`ingestJobStore.ts`: localStorage index `lampas-agent-clips.jobs` + OPFS dir `agent-clips-jobs`). Resume via `planJobResume()` (`ingestJob.ts:83`). Batch limits: `LABEL_BATCH_SIZE = 4`, `LABEL_CONCURRENCY = 1` — both tuned around the 60 s ALB idle timeout. Failure handling: `stage()` dispatches `fail`, `jobStatusAfterFailure()` sets `error`, and `IngestFlow.onFailed` only calls `refresh()` — **the queue stops and waits for a human**; retry is a manual button (`setRunToken`). Cost tracking is only `fetchCreditBalance()` shown in `ClipsHeader.tsx`; no per-job estimate or accounting.

## 5. Scheduling

`watchCommand()` (commands.ts:366) is a `for` loop with `setTimeout` in a foreground process; `--every` parsed by `parseIntervalMs`. There is **no launchd/cron unit for `watch`** — only `io.lampas.agent.daemon.plist` running `serve`. On daemon restart mid-mission: Chrome is killed, the mission row stays `RUNNING`, partially collected `contents` survive, selections are lost, and nothing resumes. Clips jobs survive (OPFS) but only in that one browser profile and only when someone reopens the tab.

## 6. State &amp; persistence

MySQL `pulse` (`missions`, `contents`, `content_metrics`, `selections`, `agent_steps`) or production via ingest; `data/sessions/&lt;platform&gt;.json`; browser `localStorage` (job index, auth tokens per target, url history, domain/category) and OPFS media. Idempotency keys: `UNIQUE(platform, content_id)`, `UNIQUE(mission_id, content_row_id)`, `UploadCheckpoint{sourceId, uploadedCount}`, `sourceS3Key`, `wikiDone`. `uploadClip()` itself has **no idempotency key** — a crash between the HTTP call and the checkpoint write duplicates a clip.

## 7. Configuration

`store/config.ts`: `loadDotEnv()` is a hand-rolled parser (no `export`, no quotes/multiline, never overrides), `resolveDbConfig()`, `resolveRemoteConfig()`, `parsePulseEnv()`. Server env: `AGENT_PORT/HOST/PROXY_TARGET/BANK_PROXY_TARGET`, `PULSE_DB_*`, `PULSE_API_URL/TOKEN`, `PULSE_CHROME_PATH`, `PULSE_ENV`. Web env: `VITE_API_URL_*`, `VITE_GOOGLE_CLIENT_ID`, `VITE_FORCE_BANK_TARGET`, `VITE_LABEL_MODEL`, `VITE_LABEL_CONCURRENCY`. **`env/.env.local` and `env/.env.production` are git-tracked and contain the MySQL password and `PULSE_API_TOKEN`.** New platform = adapter + `ALL_PLATFORMS`/`PulsePlatform` in `mission.ts` + `ADAPTERS` in `registry.ts`. A new "topic" is just a CLI string — `buildQueries()` is hardcoded per adapter ("쇼츠"/"shorts"), so topics are not configurable data.

## 8. Tests

30 vitest files. Web pure libs are well covered (`labelBatch` 27, `ingestJob` 27, `transcribe` 25, `apiTarget` 21, `runIngestPipeline` 19). Server coverage is thin: `cliArgs.spec` (9), `serveLib.spec` (10), `store/config.test` (8), `deck/tools.test` (5), `extract/instagram.test` (4), `mission.test` (3), `runner.test` (2 — only `planSearchTargets`). **Untested:** `runPlatformAgent` loop, all four adapters (no HTML fixtures), `PulseStore`/`RemotePulseStore`, `serve.ts` routing/proxy/WS, `PulseSession`, `commands.ts`, and every React component.

## 9. Known pain points (LOG.md / CLAUDE.md / spec)

504s from the 60 s ALB idle timeout (batch 8→4, concurrency 6→1); label JSON parse failures and empty descriptions; empty ASR results needing a second model; a type bug only `tsc` caught; a knowingly-unfixed `audioExtract.ts` type error (spec "비범위"); the leaked `PULSE_OPENAI_API_KEY` in git history; Tailscale Funnel registration fragility; X/Threads adapters marked experimental/unverified.

## Structural weaknesses

1. **No persistent job/queue table for Pulse** — `runner.ts`/`mission-run.ts` hold candidates in memory only; a restart loses in-flight missions and strands `missions.status = 'RUNNING'` rows (`store/db.ts`).
2. **No LLM tool-calling loop; the "Agent Loop" is hard-coded** — `agent/runner.ts:59` is a fixed scroll-then-visit script, phases are log strings, and the one LLM-shaped hook (`PulseSession.observe()`, `deck/session.ts:143`) is dead code. Selection is a fixed formula in `select.ts`.
3. **Unauthenticated remote browser control on a public URL** — `serve.ts:139-210` accepts any `/pulse/ws` peer and injects clicks/keys into a Chrome holding platform login cookies; `scripts/setup-tailscale.sh --funnel` is the documented live mode.
4. **Single global `PulseSession` broadcast to all clients** (`serve.ts:111` + `deck/session.ts`) — no per-client session, no input arbitration, one mission max system-wide.
5. **No retry/backoff/rate-limit awareness anywhere in collection** — `runner.ts:185` swallows per-candidate errors; `RemotePulseStore.request()` (`store/remote.ts:36`) has no retry, and `logStep` issues one HTTP POST per log line fire-and-forget (`runner.ts:73`), silently dropping the step log on failure.
6. **Clips orchestration lives in a browser tab** (`runIngestPipeline.ts` + `IngestFlow.tsx`) — work only progresses while that tab is open on that profile; nothing is visible or resumable server-side.
7. **The clips queue stalls on any error** — `IngestFlow.onFailed` (`IngestFlow.tsx:319`) only refreshes; there is no auto-advance to the next job and no automatic retry.
8. **Non-idempotent clip upload** — `uploadLabeledClips()` (`runIngestPipeline.ts:263`) checkpoints *after* `uploadClip()`, so a crash in between duplicates clips; there is no server-side dedup key.
9. **Scrapers are regex-over-HTML with no fixtures or contract tests** — `agent/platforms/instagram.ts:75-95`, `threads.ts:44-50`, `x.ts:38-56` will silently degrade to `skip: '메타데이터 없음'` on any markup change, and nothing alerts on a drop in yield.
10. **`watch` has no supervision** — `commands.ts:366` is an in-process `setTimeout` loop with no launchd unit, no lockfile (two `watch`es can interleave into the same DB), and no per-cycle state.
11. **Hand-rolled proxy with no timeouts or limits** — `proxy()` (`serve.ts:37`) can hang connections indefinitely and forwards all headers upstream; combined with Funnel it is an open relay to `127.0.0.1:3133`.
12. **Secrets committed and config unvalidated** — `env/.env.local`/`.env.production` are git-tracked with the DB password and `PULSE_API_TOKEN`; `loadDotEnv()`/`resolveDbConfig()` (`store/config.ts`) do no schema validation and default silently to `root`@127.0.0.1.
13. **Migrations are `CREATE IF NOT EXISTS` + swallowed `ALTER`s** (`store/db.ts:175-182`) — schema drift between local and the production ingest DB fails silently.
14. **Server-side test coverage gap** — no test exercises `runPlatformAgent`, any adapter, either store, or `serve.ts`, so the riskiest code has the least protection.</result>
<usage><subagent_tokens>160304</subagent_tokens><tool_uses>31</tool_uses><duration_ms>190185</duration_ms></usage>
</task-notification>

## assistant

lampas-agent 분석이 도착했습니다. omnara 분석이 오면 두 결과를 합쳐 개선안을 정리하겠습니다.

## user

<task-notification>
<task-id>ac9e74a25f26f409c</task-id>
<tool-use-id>toolu_01JQVt6LG1T463fYK8RpRUAy</tool-use-id>
<output-file>/private/tmp/claude-501/-Users-progdesigner-Works-lampas-lampas-system/81e5186a-9adc-4ea3-ae60-2d50fc174ca0/tasks/ac9e74a25f26f409c.output</output-file>
<status>completed</status>
<summary>Agent "Analyze omnara agent runtime design" finished</summary>
<note>A task-notification fires each time this agent stops with no live background children of its own. The user can send it another message and resume it, so the same task-id may notify more than once.</note>
<result>I have a complete picture. Here is the report.

---

# Omnara architecture — how it runs durable agents

## 1. Agent lifecycle &amp; durability

**The core idea: the agent is a database state machine, not a process.** There is no long-lived "agent process". A stateless worker pool claims work, does exactly one step, commits, and releases.

**Tables** (`migrations/000004_events_messages_turns.sql`, `000002_agents.sql`, `000005_model_context_tools_policy.sql`):

- `agents` — `state ∈ ('active','archived')`, `next_event_sequence` (monotonic per-agent counter), `idempotency_key` unique per project. An `agents_identity_immutable` trigger rejects changes to id/org/project/idempotency_key.
- `agent_events` — the append-only log. `event_kind ∈ ('agent_input','model_output','tool_result','context_checkpoint')`, `UNIQUE(agent_id, sequence)`, `UNIQUE(agent_id, idempotency_key)`, and a `reject_agent_event_mutation()` trigger that raises on any UPDATE/DELETE. Events are genuinely immutable at the DB level.
- `agent_inputs` — the inbox. `state ∈ ('received','resolved','rejected','canceled')`, `input_kind ∈ ('content','control','interaction_response','config_change')`, `delivery_mode ∈ ('queued','steering','immediate')`. Enormous CHECK constraints encode the legal (kind, mode, state) combinations so illegal states are unrepresentable. An input becomes part of history only by being *admitted*: `admitted_event_id` must point at an `agent_input` event whose `idempotency_key = 'agent_input:' || input.id` (enforced by `enforce_agent_input_admitted_event`).
- `agent_turns` — `turn_sequence`, `latest_event_id`, `latest_semantic_event_id`. Every turn must have an opening event (deferred constraint trigger `agent_turns_opening_event_required`).
- `agent_wakeups(agent_id PK, ready_at)` — the scheduling frontier. One row per agent that has something to do.
- `agent_runtime_locks` — `UNIQUE(agent_id)`, `worker_process_id`, `lease_expires_at`, `cancel_requested_at`. This is the mutual-exclusion + crash-recovery primitive.
- `model_call_contexts` — a durable record of *one model call attempt*: `operation_kind ∈ ('normal','compaction')`, `attempt_number`, `state ∈ ('started','succeeded','failed','canceled')`, `recovery_kind ∈ ('retry','compact','reduce_compaction_source')`, `retry_at`, plus full token accounting (`uncached/cache_read/cache_write/output/reasoning`) with a CHECK that the parts sum to `input_tokens_total`.
- `tool_calls` — `state ∈ ('awaiting_authorization','awaiting_permission','ready','running','waiting','completed')`, with `CHECK ((state='running') = (runtime_lock_id IS NOT NULL))` — a running tool is *provably* owned by a live lease.

**The loop** (`internal/harness/worker/worker.go`, `internal/harness/kernel/agent_executor*.go`):

`Worker.Run` starts `capacity` goroutines each running `runLoop` → `RunOnce`. `RunOnce` calls `store.ClaimNextAgentWork` (`internal/storage/executionstore/agent_inputs_store.go:100`), which in **one transaction**:

1. `ClaimNextAgentWakeup()` — SKIP-LOCKED style pop of the due-wakeup index.
2. Decide the single next step, in priority order: pending **tool work** → incomplete tool batch (wait) → **steering** inputs → ready **model work** → retry-wait → **queued** inputs → idle.
3. `acquireAgentRuntimeLockTx` — take the per-agent lease.
4. Admit inputs + open a turn atomically if starting fresh.

It returns `ClaimedAgentWork{Kind: AgentWorkModel|AgentWorkTool, RuntimeLock, Model: ClaimedModelWork{Kind: ModelWorkStart|Resume|Continue, ...}, Tool: ClaimedToolWork{...}}`. The executor then runs `ExecuteModelWork` or `ExecuteToolWork` (`internal/harness/kernel/types.go`). Crucially there is a hard invariant check: *"agent has both tool work and model work for its current frontier"* is an error, not a tolerated race.

**Recovery.** Nothing is in memory. A crashed worker's lease simply expires; `cmd/maintenance` runs `ReapExpiredAgentRuntimeLocks` (batch 100) on a jittered ticker, and the next claim recovers via `ModelWorkResume` (a `started` context with no output) or `ModelWorkContinue`. The lock is renewed in-flight by `RenewAgentRuntimeLock`, and a failed renewal sets `renewalFailed` on the `activeRuntime` so the in-flight work self-cancels rather than double-writing. Tests named `TestClaimNextAgentWorkRecoversUnstartedTurnAfterRuntimeRelease` / `RecoversFailedAbandonedContext` / `RecoversAmbiguousAbandonedContext` document the recovery matrix.

**Idempotency** is layered: `agents.idempotency_key`, `agent_events.idempotency_key` (`'agent_input:'||input.id`), `agent_inputs (idempotency_scope, input_idempotency_key)`, `tool_calls UNIQUE(agent_id, model_output_id, provider_call_id)`, and cron's `fireIdempotencyKey = "cron_trigger:"+id+":"+dueAt.RFC3339`.

## 2. Event model

`internal/events/event.go` is deliberately tiny — `Kind` (4 values above) plus `Event{ID, AgentID, Sequence, Kind, At, IdempotencyKey}`. Bodies live in side tables joined by kind. Projection to the public API is in `internal/httpapi/publicevents/publicevents.go` (`EventFromReadRecord`, `ModelOutputContentBlocks`, `ToolResultContentBlocks`); content blocks are `text | media_ref | tool_call | structured_data`.

Delivery is **SSE**, not WS: `internal/httpapi/agent_runtime_routes.go` sets `text/event-stream`, flushes per frame, and emits `": heartbeat\n\n"` on `agentEventStreamHeartbeatInterval`. Two independent liveness paths: Postgres LISTEN/NOTIFY fanout (`internal/notifications/fanout.go`, `AgentStreamDeltaPublisher`) for low latency, plus `internal/httpapi/agent_event_reconciler.go` — a 10s poll of `ListAgentEventFrontiers` per subscribed agent that catches any missed notification. Notifications are `PostCommitIntent`s (`AgentEventCommitted`, `ToolCallUpdatedCommitted`) published only after the tx commits.

Client: `frontend/packages/sdk/src/agent-event-stream.ts` — `openAgentEventStream` is an async generator that keeps a durable `cursor` (the last `sequence`), reconnects with `Last-Event-ID`, dedupes (`if (sequence &lt;= cursor) continue`), uses full-jitter backoff (1s → 30s), honors `Retry-After`, resets the failure counter after 10s of stability, and enforces a 35s **stall timeout** deliberately set above the server heartbeat. Only `transport`, `service_unavailable`, 408/429/5xx are retried.

## 3. Tools &amp; skills

`agent.yaml`/JSON schema is `AgentConfigSource` in `internal/agentconfig/source_schema.go`:

```go
type AgentConfigSource struct {
    Version, Instruction string
    Model          AgentConfigModelSource      // provider_config, name, context_window_tokens, cache_retention, reasoning.effort
    MachineSources []AgentConfigMachineSource  // machine_pool_name, cwd, env_overlay, secret_env_overlay
    Tools          map[string]AgentConfigToolSource   // enabled, permission, deferred, description, input_schema
    MCP            map[string]AgentConfigMCPSource    // url, auth{type,secret_id}, permission, deferred, tools{}
    Skills         []string
    Subagents      map[string]AgentConfigSubagentSource
    MaxSubagents, MaxDepth *int
}
```

Configs are validated against a compiled JSON Schema and are **immutable** once created; you make a new config and repoint the profile with `expected_current_config_id` (optimistic concurrency).

Built-ins live in `internal/toolcatalog/names.go`: `run_command`, `read/write/stop/list_process(es)`, `create/delete/list/inspect_machine`, `ask_question`, `send_integration_message`, `set_integration_target`, `web_search`, `web_fetch`, `read_file`, `search_files`, `upload/download_file`, `skill`, `spawn_agent`/`read_agent`/`send_agent_message`/`stop_agent`/`list_agents`, `tool_search`, `call_deferred_tool`.

Registration (`internal/harness/tools/registry.go`) is a startup-validated contract: every catalog entry must have an implementation and vice versa, every input schema must compile, every declared permission mode must have a handler, and each tool must supply a `Transactional` and/or `Async` phase. `ValidateToolImplementationRegistry()` makes drift a boot failure.

Permissions (`internal/toolpermission/modes.go`): `always_allow | always_ask | always_deny`. `always_ask` creates an `agent_interactions` row and parks the tool call.

**Deferred tools + `tool_search`** is the standout idea: tools marked `deferred: true` are not in the model's tool array. The model calls `tool_search{pattern, max_results}` (default 5, max 50) to pull schemas in on demand, then `call_deferred_tool`. This keeps large MCP catalogs out of the context window.

Truncation: `internal/textutil/truncate.go` (`TruncateRunes`, `TruncateBytes`), `processaction.MaxObservationBytes = 64KB` / `DefaultObservationBytes = 8KB` / `MaxWriteBytes = 128KB`, `toolcatalog.ArtifactPageBytes = 4KB` with `MaxReadableArtifactBytes = 48MB`. The pattern for overflow (see `tool_result_overflow_integration_test.go`, `content_parts.go`): store the full payload as a **blob artifact**, return a truncated text part plus a `media_ref` content block with an `artifact_id`, and let the model page it back with `read_file`.

Skills are `SKILL.md` + files in a tar.gz/zip, content-addressed by `sha256:` digest (`internal/skills/archive.go`), broadcast to machines over the daemon socket and verified before extraction, with an `extractionBudget` guarding zip-bombs.

## 4. Machines / daemon

`internal/daemonprotocol/protocol.go` — a typed WS envelope `{type, payload}`, `MaxMessageBytes = 1MB`. Message types: `heartbeat`/`heartbeat_ack`, `process_offer`/`process_accept`/`process_accept_ack`/`process_terminate`, `action_offer`/`action_accept`/`action_accept_ack`, `report`/`report_ack`, `error`, `runtime_ended`, `skill_offer`/`skill_report`.

The key pattern is **offer → accept → ack**, never "command → run". The backend *offers* a `ProcessOffer{ProcessID, Command, ShellSelector, Cwd, Env, TimeoutSeconds}`; the daemon accepts, the backend acks with a grant. `ActionGrant` carries `DefaultOutputCursor` so reads resume exactly. `AckStatus ∈ {committed, cleanup_only, permanent_reject, transient_error}` tells the daemon whether to retry, drop, or clean up — the ack itself distinguishes "we persisted it" from "you may safely forget it".

Heartbeat interval is **server-directed**: `heartbeat_ack.next_heartbeat_after_ms` resets the daemon's timer (`internal/machinedaemon/client_socket.go`), so the server can throttle without a client deploy. Reconnect (`client.go`) is a loop: on disconnect, *reconcile local state first, then reconnect*, with `retryBackoffDelay(initial, maximum, failures)`. Reports the daemon couldn't deliver go to a **local outbox** (`report_outbox.go`, `replayReportOutbox`) and are replayed on reconnect — at-least-once delivery with server-side idempotency by `ReportID`. `ProcessState ∈ {queued, starting, running, exited}`.

## 5. Scheduling

`internal/cronschedule/cronschedule.go` uses `robfig/cron/v3` with 5-field parsing and an explicit `timezone` field (`TZ=` prefixes are rejected — timezone is data, not syntax). `internal/crontrigger/service.go`: `FireDueTriggers` → `ClaimDueCronTriggers(batch 100)` returns `{Claimed, Disabled}` (unparseable schedules are auto-disabled), each claim carries a `ClaimToken` for the completing write. Targets are `profile` (launch a fresh agent) or `agent` (send an input to an existing one). Failures call `RecordCronTriggerFailure{WillRetry}` where `permanentFireError` = NotFound | InvalidRequest | IdempotencyConflict. `message_template` is a sandboxed `text/template` (`internal/cronschedule/template.go`): 64KB in/out cap, 1s render timeout, bounded printf width.

## 6. Compaction

`internal/compaction/planner.go` — `RecentTailTargetTokens(usable) = min(usable/4, 20_000)`: always keep a verbatim recent tail of ~25% of the window. `SelectRetainFromEventSequence` picks the retain boundary by preference: whole-turn boundary → whole-turn-ish boundary → nearest safe event boundary. "Safe" means never splitting an `AtomicGroup{Kind, StartSequence, EndSequence}` — a tool-call/tool-result pair can never be severed.

Summaries are **cumulative append-only checkpoints** (`context_checkpoint` events), not rewrites. `SummaryPrompt` (`prompts.go`) forces a fixed structure — `## Goal / ## Instructions / ## Progress / ## Relevant Artifacts / ## Next Steps` — and explicitly instructs "summarize state only; newer instructions supersede older ones".

`BoundedProgressiveCheckpointPolicy` (`progressive.go`) caps consecutive checkpoints at **3** and refuses a checkpoint that doesn't advance the frontier or that leaves no model-visible source — a hard guard against compaction death-spirals. Compaction runs as its own `model_call_contexts` row with `operation_kind='compaction'` so it is itself crash-recoverable and separately retryable (`recovery_kind='reduce_compaction_source'` shrinks the source range and retries).

## 7. Human-in-the-loop

`agent_interactions(tool_call_id, interaction_kind ∈ ('permission','question'), state ∈ ('open','resolved','canceled'), request jsonb, resolution jsonb, resolved_by_input_id)` with `UNIQUE(agent_id, tool_call_id, interaction_kind)`. A tool needing approval parks in `awaiting_permission`; the answer arrives as an `agent_inputs` row of kind `interaction_response` with `delivery_mode='immediate'`, which resolves the interaction and unparks the tool — all through the same durable input pipeline. `ask_question` (`interaction_tools.go`) is the same machinery with a structured form (`internal/interactionform`).

Slack (`internal/integration/slack/`) is a first-class *bidirectional* surface: `send_integration_message` delivers, thread replies become agent inputs, and approvals/questions render as Block Kit prompts (`prompt.go`, `PromptActionValue{InteractionID, AgentID, IntegrationTargetID}`) with button clicks posting back as interaction responses.

## 8. Examples (signal-collection agents)

All three are a single runnable jupytext `demo.ts` (Deno/Node/Bun) that *provisions* the agent through the SDK rather than hosting it. Shape is identical: resolve org/project → upsert API key as a project **secret** (referenced only by `sec_…` id) → build one config object `{instruction, model, mcp|machine_sources, tools}` → `createAgentConfig` + `createAgentProfile` → `createAgent` with a kickoff message → stream events → optional Slack setup → optional cron trigger.

- **posthog-pulse-agent** — no machine at all. Tools are just PostHog's hosted MCP (URL-level feature filter `?mode=tools&amp;features=insights,data_schema,sql` makes write tools non-existent) + `send_integration_message`. Daily 9am pulse.
- **x-signal-agent** — has a machine (`machine_sources: [{machine_pool_name, secret_env_overlay: {X_BEARER_TOKEN: secretId}}]`) and uses `run_command` + curl against the X API, plus `web_search`/`web_fetch` for author verification. Weekday `0 9 * * 1-5`.
- **reddit-signal-agent** — Apify MCP scraper; instruction encodes the async poll protocol (start actor → poll `get-actor-run` with `waitSecs 45` until SUCCEEDED → `get-dataset-items` with explicit `limit 200` and offset paging).

The prompt discipline is the reusable part: **a hard call budget** ("exactly one search request per run", "stay under 8 MCP calls"), **no retry loops** ("if it fails, report the body and stop"), **an explicit empty outcome** ("nothing worth your time today" / "steady day" is a good result — never pad), a fixed per-item output schema, and **statelessness between runs** (recompute the 7-day baseline every time; no cross-run state to corrupt).

## 9. Observability

`internal/log` is span-like structured logging, not line logging: `log.NewEvent(ctx, "worker.loop")` returns an `*Event` that accumulates fields and child traces (`AttachDBQuery`, `AttachHTTPRequest`, capped at `maxTraceRecords = 25`) and emits **one** record at `event.Done(ctx)`. `internal/log/logent/` is a per-domain vocabulary of typed attachers (`logent.WorkerLoop`, `AgentWorkScope`, `RuntimeLock`, `Turn`, `AgentInput`) so field names can't drift. `log.Level(ctx, DebugLevel)` downgrades boring outcomes retroactively (idle loop iterations). `internal/log/scrub.go` redacts secrets.

`internal/metrics/` has Prometheus collectors per subsystem (db, httpapi, httpclient, daemon, notifications, provider_runtime, ready). Cost/usage is per-`model_call_context` token accounting plus migration `000013_model_call_provider_cost`. `internal/blackbox` is a build-tagged suite that tests a *deployed* instance through the public HTTP API only — no internal imports, no DB access.

---

## Ranked: 10 most reusable ideas for lampas-agent

1. **Append-only event log + derived state, with the log as the only source of truth.** Even in SQLite: `events(id, run_id, seq UNIQUE, kind, at, idempotency_key UNIQUE)` with kinds `input | model_output | tool_result | checkpoint`. Every restart replays. Directly applicable to a clip pipeline where each segment/transcription/label is an event.
2. **Lease-based single-runner locking instead of in-process state.** `runtime_locks(run_id UNIQUE, worker_id, lease_expires_at)` + a renewer + a reaper. This is what makes crash-recovery free and stops two `ffmpeg`/Playwright workers from stomping the same clip. Also gives you cooperative cancel via `cancel_requested_at`.
3. **`claimNextWork()` as one transaction that returns exactly one step.** A single prioritized decision function (`tool work → model work → queued input → idle`) that the worker loop calls in a loop. Keep the invariant checks loud — "both tool work and model work" should throw, not be tolerated.
4. **Idempotency keys everywhere, composed from stable identity.** `"cron_trigger:"+id+":"+dueAt.toISOString()` is the pattern to copy verbatim for scheduled scrapes; for clips use `"segment:"+videoId+":"+startMs`. Makes at-least-once delivery safe everywhere and re-running a scrape a no-op.
5. **Per-attempt durable "call context" rows.** A `model_calls`/`job_attempts` table with `state ∈ started|succeeded|failed|canceled`, `attempt_number`, `recovery_kind ∈ retry|compact|reduce_source`, `retry_at`, and token counts. This is what turns "retry logic" from ad-hoc try/catch into inspectable state, and gives you cost tracking for free. Pair with a `Decide(err, attempt) → {retry|stop, delay}` policy module (`internal/modelretry/policy.go`) that honors provider `Retry-After` and treats deterministic input failures as non-retryable.
6. **Two-phase tool handlers: `Transactional` then `Async`.** Commit the intent (tool call recorded, state → `running`) in a transaction, then do the slow work (download, ffmpeg, Whisper, browser nav) outside it, then commit the result. `tool_calls.state` with `CHECK((state='running') = (lease_id IS NOT NULL))` means an orphaned `running` row is impossible. This is *the* pattern for long-running media work.
7. **Overflow to artifacts, not to truncated strings.** Never put a transcript or a page dump in the model's context. Write the blob, return `{text: truncated, media_ref: {artifact_id}}`, and expose a paged `read_file` (4KB pages) so the model pulls only what it needs. Combine with explicit byte caps (64KB observation, 8KB default) enforced in the tool layer.
8. **Compaction as cumulative structured checkpoints with atomic-group boundaries.** Keep `min(window/4, 20k)` tokens of verbatim tail, never split a tool-call/result pair, use the fixed `Goal/Instructions/Progress/Artifacts/Next Steps` summary prompt, and **cap consecutive compactions at 3** with a "does it advance the frontier?" guard. The bounded-progressive policy is the single best anti-footgun here.
9. **Deferred tools + `tool_search`.** With a browser driver, ffmpeg wrappers, and labeling tools you will blow past a sane tool count. Mark most `deferred`, expose a `tool_search(pattern, max_results=5)`, and let the model pull schemas on demand. Cheap to implement, large context savings.
10. **A resumable SSE/stream client with a durable cursor.** Copy `openAgentEventStream` almost literally: cursor on `sequence`, `Last-Event-ID` on reconnect, dedupe `sequence &lt;= cursor`, full-jitter backoff 1s→30s, a stall timeout set *above* the server heartbeat, and a retryable-error predicate. Same shape works for your local UI and for driving a browser worker.

Runner-up worth stealing: the **offer → accept → ack** protocol with a local replay outbox and `AckStatus ∈ {committed, cleanup_only, permanent_reject, transient_error}` — if lampas ever splits into a controller plus a browser/media worker process, this is the contract to use, and the server-directed `next_heartbeat_after_ms` lets you retune liveness without redeploying the worker.</result>
<usage><subagent_tokens>127568</subagent_tokens><tool_uses>63</tool_uses><duration_ms>346837</duration_ms></usage>
</task-notification>

## assistant

두 분석이 모두 끝났습니다. 핵심 주장 몇 가지를 코드에서 직접 확인한 뒤 정리하겠습니다.

## assistant

확인 결과 핵심 주장은 모두 코드와 일치합니다(서버 쪽 LLM 호출 0건, `/pulse/ws` 무인증, `env/.env.local`·`.env.production` 이 비밀번호·토큰과 함께 git 추적). 아래는 omnara 의 설계와 대조한 개선안입니다.

## omnara 가 다르게 하는 것

omnara 의 핵심은 "에이전트는 프로세스가 아니라 DB 상태 머신" 입니다. 상태 없는 워커가 트랜잭션 하나로 다음 한 단계를 claim 하고, 실행하고, 커밋합니다. 그래서 크래시 복구가 공짜입니다. 이를 받치는 부품은 다섯 개입니다.

- **append-only 이벤트 로그** (`agent_events`, DB 트리거로 UPDATE/DELETE 금지, `UNIQUE(agent_id, idempotency_key)`).
- **리스 기반 런타임 락** (`agent_runtime_locks`, `lease_expires_at` 만료분을 maintenance 가 회수).
- **시도 단위 durable 레코드** (`model_call_contexts`: attempt_number·state·recovery_kind·retry_at·토큰 수).
- **2단계 툴 실행** (Transactional 로 의도 커밋 → Async 로 느린 작업 → 결과 커밋, `running` 이면 반드시 락 보유라는 CHECK).
- **멱등키를 안정적 정체성으로 조합** (`cron_trigger:{id}:{dueAt}` 같은 식).

여기에 컨텍스트 관리(누적 체크포인트, 연속 압축 3회 상한, 툴콜/결과 쌍 절대 분리 금지), 지연 툴 + `tool_search`, 커서 기반 재접속 SSE 클라이언트, 사람 개입(`agent_interactions` + Slack 양방향)이 얹혀 있습니다.

## lampas-agent 의 현재 약점

Pulse 와 Clips 모두 "에이전트" 라기보다 고정 파이프라인 스크립트입니다.

- **Pulse 는 LLM 을 전혀 쓰지 않습니다.** `agent/runner.ts` 의 OBSERVE/DECIDE/ACT 는 로그 라벨일 뿐이고, 스크롤→방문→정규식 추출→`select.ts` 고정 점수식입니다. `PulseSession.observe()` 는 호출자 없는 죽은 코드입니다.
- **재개 불가.** 후보 큐는 메모리에만 있고 selections 는 끝에서만 씁니다. 데몬 재시작 시 `missions.status='RUNNING'` 행이 영구히 남고 선별 작업은 사라집니다. `watch` 는 포그라운드 `setTimeout` 루프라 launchd 유닛도, 락파일도 없습니다.
- **재시도·백오프·429/캡차 감지가 없습니다.** 후보별 `try/catch` 로 삼키고 넘어가며, `RemotePulseStore` 도 재시도 없이 로그 한 줄당 POST 하나를 fire-and-forget 합니다.
- **Clips 오케스트레이션이 브라우저 탭 안에 있습니다.** 탭이 열려 있는 그 프로필에서만 진행되고, 실패하면 큐가 멈춰 사람을 기다립니다(`IngestFlow.onFailed` 는 refresh 만 호출). `uploadClip()` 은 체크포인트를 업로드 뒤에 쓰므로 그 사이 크래시면 클립이 중복됩니다.
- **보안.** Funnel 로 공개된 URL 에서 `/pulse/ws` 가 인증·origin 검사 없이 인스타/X 로그인 쿠키를 가진 Chrome 에 클릭·키 입력을 주입할 수 있습니다. `proxy()` 는 타임아웃·크기 제한 없이 `127.0.0.1:3133` 으로 여는 릴레이입니다. 비밀번호·`PULSE_API_TOKEN` 이 든 env 파일이 git 에 추적됩니다.
- **테스트 공백.** 어댑터 4개, 러너 루프, 스토어 2종, `serve.ts` 라우팅/프록시/WS, `PulseSession` 이 전부 무테스트이며 HTML fixture 가 없습니다.

## 개선 제안 (우선순위순)

1. **보안 먼저.** `/pulse/ws` 와 `/v1` 프록시에 토큰(env 의 `AGENT_ACCESS_TOKEN`) 또는 Google JWT 검사와 origin 화이트리스트를 넣고, 프록시에 타임아웃·바디 크기 제한을 둡니다. `env/.env.local`·`.env.production` 을 git 에서 빼고(`.example` 만 추적) 노출된 DB 비밀번호·토큰을 교체합니다.
2. **로컬 SQLite 잡 원장.** `runs(id, kind, status, idempotency_key UNIQUE)` + `events(run_id, seq, kind, payload)` + `attempts(run_id, step, attempt_number, state, retry_at, error)` + `locks(run_id UNIQUE, lease_expires_at)`. Pulse 미션과 Clips 잡을 같은 원장에 올리면 재시작 복구·중복 방지·비용 집계가 한 번에 해결됩니다. OPFS·localStorage 는 캐시로 격하합니다.
3. **`claimNextWork()` 워커 루프.** 데몬 안에 워커를 두고 "툴 작업 → 모델 작업 → 대기 입력 → idle" 순으로 한 단계씩 claim·실행·커밋합니다. `watch` 는 별도 프로세스 대신 원장에 `cron:{missionId}:{dueAt}` 멱등키로 wakeup 행을 넣는 스케줄러로 바꿉니다.
4. **재시도 정책 모듈.** `decide(err, attempt) → {retry|stop, delayMs}` 순수 함수 하나로 429·5xx·네트워크·캡차를 분류하고 `Retry-After` 를 존중합니다. 결정적 실패(파싱 실패 등)는 즉시 stop. 어댑터별 수집 yield 가 급감하면 알림을 냅니다.
5. **Clips 파이프라인을 서버 워커로 이관.** 브라우저는 UI 만 담당하고, 단계별 idempotency 키(`segment:{sourceId}:{startMs}`, `upload:{sourceId}:{clipIndex}`)를 원장에 기록한 뒤 업로드합니다. 서버 `POST /clips` 에도 같은 키를 받아 중복 삽입을 거부하게 하면 이중 안전입니다.
6. **Pulse 에 LLM 결정 루프 도입.** omnara 예제(`x-signal-agent`, `reddit-signal-agent`) 의 프롬프트 규율을 그대로 씁니다. 호출 예산 명시("탐색 호출 8회 이하"), 재시도 루프 금지, "오늘은 볼 것 없음" 을 정상 결과로 허용, 고정 출력 스키마, 실행 간 무상태. 툴은 `search_feed`·`open_detail`·`extract`·`select` 정도로 시작하고, 정규식 추출 결과가 비면 LLM 비전으로 페이지를 읽는 폴백만 붙여도 X/Threads 어댑터 취약성이 크게 줄어듭니다.
7. **긴 관측값은 아티팩트로.** 전사문·페이지 덤프를 프롬프트에 그대로 넣지 말고 파일로 저장한 뒤 잘린 텍스트 + 참조 id 를 주고, 페이지 단위 `read_artifact` 툴로 필요한 만큼만 읽게 합니다. 지금 라벨 배치가 4개로 묶인 원인인 60초 ALB 타임아웃 압박도 줄어듭니다.
8. **구조화 로그.** `missionId`·`runId`·`step`·`attempt` 를 담는 이벤트 단위 로그로 바꾸고 `mission:log` 문자열을 정규식으로 다시 파싱하는 코드(`session.ts` 의 `미션 #N 시작` 매칭)를 없앱니다.
9. **Control Deck 세션 분리.** 전역 싱글턴 `PulseSession` 을 클라이언트별 세션으로 나누고, 스크린캐스트에 프레임 스로틀·백프레셔를 둡니다.
10. **테스트.** 어댑터별 HTML fixture 계약 테스트, 워커 루프의 복구 매트릭스 테스트(시작 전 크래시·실행 중 크래시·결과 커밋 전 크래시), `serveLib` 수준으로 프록시·WS 인증 로직을 순수 함수로 뽑아 vitest 로 덮습니다.

1~2번은 지금 코드에 바로 붙일 수 있는 범위이고, 3·5·6번이 "효과적인 에이전트 업무" 로 가는 구조 변경입니다. 6번의 LLM 루프는 lampas-api `/v1/ai` 게이트웨이를 통해 과금까지 일원화하면 됩니다.

이 내용을 팀과 공유할 페이지로 정리하거나, 1~2번 보안 항목부터 바로 구현에 들어갈 수 있습니다.

## user

구현해고 구현이 끝나면 에이전트 재작해줘

## assistant

구현에 들어갑니다. 먼저 관련 서버 코드를 읽어 원장·워커·인증을 어디에 붙일지 정합니다.

## assistant

지금은 설계를 확정하고 코드를 쓰기 시작합니다. 서버 기반 모듈(로그·재시도·인증·프록시)부터 만듭니다.

## assistant

설계가 잡혔습니다. 기반 모듈 4개(구조화 로그·재시도 정책·접근 토큰/오리진 검사·프록시 헤더 필터)와 테스트를 먼저 작성합니다.

## assistant

기반 모듈 테스트 28개 통과. 다음은 SQLite 잡 원장(runs·events·schedules)과 claim/스케줄 순수 로직, 그리고 워커 루프입니다.

## assistant

원장 테스트 통과. 이제 워커 루프와 AI 모듈(게이트웨이·검색어 계획·교차 심사·비전 폴백·차단 감지)을 작성합니다.

## assistant

모듈 테스트 전부 통과(누적 71개). 이제 러너·미션 실행·원격 스토어에 AI 컨텍스트·차단 감지·재시도를 연결합니다.

## assistant

전체 336개 테스트와 타입체크가 통과했습니다. 빌드와 데몬 스모크 테스트(인증·프록시·원장 API·CLI)를 진행합니다.

## assistant

테스트 337개·타입체크 통과. 이제 배포 스크립트로 빌드하고 launchd 데몬을 재기동합니다.

## assistant

구현과 배포(데몬 재기동)가 끝났습니다. 데몬은 `io.lampas.agent.daemon` v1.0.1 로 다시 떠 있고, 로컬 7452 와 공개 URL 의 `/healthz` 모두 정상 응답합니다.

**바로 필요한 조치**

- 이제 공개 URL 은 접근 토큰을 요구합니다. 쓰는 브라우저마다 아래 주소를 한 번 열어 쿠키를 받으세요. `/pulse` 에 그냥 들어가도 토큰 입력 화면이 뜹니다.

```
https://lampas-system.tail0e32ab.ts.net/agent/auth?token=[REDACTED-BY-JOHN-WIKI-INGEST — see secrets-plaintext-exposure-pattern policy]
```

- AI 단계(검색어 계획·비전 폴백·교차 심사)는 파트너 API 키가 없어 꺼져 있습니다. `apps/lampas-agent/env/.env.local` 의 `AGENT_AI_API_KEY` 에 platform.lampas.io 에서 발급한 키를 넣고 `./scripts/deploy-agent.sh lampas-agent` 를 다시 돌리면 켜집니다. 키 소유 계정에 과금됩니다.

**구현한 것** (설계 문서 `docs/superpowers/specs/2026-09-18-lampas-agent-durable-runs-design.md`)

- **잡 원장 + 워커.** SQLite `data/agent.db` 에 런·이벤트·스케줄을 기록하고, 데몬 안 워커가 런을 하나씩 claim 해 실행합니다. 리스 만료 복구, 멱등키, 지수 백오프 재시도, 취소가 원장 이벤트로 남습니다. 데몬이 재시작돼도 대기·재시도 런이 이어집니다.
- **재시도·차단 감지.** 실패를 rate_limit/blocked/network/timeout/server/deterministic 으로 분류해 정책대로 재시도합니다. 캡차·레이트리밋·로그인 리다이렉트를 보면 플랫폼 실행을 즉시 멈춥니다.
- **AI 결정 단계.** lampas-api 게이트웨이로 검색어 계획, DOM 추출 실패 시 비전 폴백, 선별 교차 심사(주제 적합도)를 미션당 예산 안에서 수행합니다. 키가 없으면 종전 수식 동작입니다.
- **접근 제어.** `/v1`·`/bank`·`/pulse/ws`·`/agent/*` 토큰 필수, WS 는 Origin 검사, 프록시는 쿠키·토큰을 업스트림에 넘기지 않고 타임아웃·본문 한도를 둡니다.
- **CLI·UI.** `pnpm pulse queue|schedule add --every 6h|runs|cancel`, Control Deck 우측에 진행/런/스케줄 탭, 결과 카드에 선별 근거와 AI 사용량. 데몬 로그는 JSON Lines 입니다.

**검증**

- 유닛 테스트 41개 파일 337개 통과, 타입체크 통과.
- 데몬 스모크: 무토큰 401, 잘못된 Origin 의 WS 403, 쿠키 발급 302 확인.
- E2E: CLI 로 등록한 런을 워커가 45초 만에 실행해 미션 #9 를 로컬 MySQL 에 저장(탐색 4, 선별 2)했습니다.

**발견한 기존 문제 2건**

- `env/.env.local` 의 `PULSE_DB_PASSWORD` 가 이 맥미니 MySQL(root 무비밀번호)과 달라 로컬 저장이 전부 거부되고 있었습니다. 빈 값으로 고쳤습니다.
- 접근 토큰이 든 env 파일은 저장소 관례상 git 에 추적됩니다. `PULSE_API_TOKEN` 과 같은 처지라 이번엔 건드리지 않았고, 정책 결정이 필요합니다.

**남긴 것**

- Clips 파이프라인은 여전히 브라우저 탭에서 돌고 업로드 멱등키가 없습니다. Control Deck 세션은 전역 싱글턴이며, 어댑터 fixture 테스트도 아직 없습니다.
- 변경은 커밋하지 않았습니다. 기존 미커밋 변경 119개 파일과 섞여 있으니 커밋 단위는 직접 정하시는 게 안전합니다.
