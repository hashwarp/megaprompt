# megaprompt

# Important Rules

Before any substantial implementation or debugging turn, attempt one narrow project-memory check after identifying the target repo and before editing files, but treat Arken Mesh/Codex Connect memory as best-effort. If memory tools fail with daemon ownership, unsafe fallback, database-owner-active, socket, or `.arken/mesh.ws` errors, do not retry in a loop and do not block the task; continue from source, logs, and runtime evidence.

Architectural default: core capsules define deterministic state, route, and model behavior; platform/runtime layers provide services, clocks, processes, browser bridges, GPU backends, and filesystem access; renderers only render snapshots and dispatch actions.

Don't write any doc files, always use the MCP commands. Do not write temp files to `/tmp` they should be written to local project `.tmp` folder. But don't put important things in tmp at all. If it's less than 5MB, just organize it properly and commit it.

You should try hard not to allow code files with more than 5000 lines, unless absolutely necessary (data files, shader files, etc). Break them into component files, util files, modules, etc. as appropriate.

Don't ask to run commands, just do whatever you can to solve the issues. If you can possibly do something, just do it and don't ask me (like for example, you can get the logs yourself, don't ask me for logs). Do not say "I need to fix X next" - JUST FUCKING FIX IT. But do not take shortcuts. Do it properly. Do not be so eager to commit and finish the turn when the request isn't fully completed. But if you struggle multiple times to do a successful proof due to competing for CPU/GPU/RAM then it's better to just stop and tell me rather than lowering the proof bar.

Clean up dangling processes after yourself, but never do a broad kill of "Dock" because that's also a system process in Mac. If your port is taken and you are killing a process, make sure it's not Codex, or let user know.

When possible, always run tests and benchmarks if available, or check captures, to confirm your changes don't break anything. You can use any version of node you want using `nvm`, and install any global packages needed. If you are doing game or UI work, you must confirm the capture is valid before you commit.

Make sure to always git commit your changes, but never commit anything larger than 100MB to the repo. Never fucking push branches without asking and getting confirmation. Don't prefix branch names with `codex`, make them logical. Use relative paths in PRs, and don't reference worktrees. Tag the commit with a revision number (eg. r100)

At the end of each turn, always send a 5s-10s video of the E2E test for me to validate, but do not spend more than 1 minute creating it, otherwise send captures. Use different filenames for each turn. If a video doesn't make sense, captures are acceptable. If the capture helper is not working, do not jump to the conclusion it’s your script or methodology that’s the issue - it’s more than likely the changes you made causing a lock-up or error. Never send captures of a loading screen, use OCR and check for "loading" near center. Not valid proofs: loading screens, errors, broken interfaces.

If you run into an issue, don’t checkpoint things separately, only checkpoint when everything planned is working as intended.

If you completed a turn with accepted proof from a capture method and are suddenly having issues, do not change the capture method, it's a code regression from your changes. DO NOT QUESTION THE CAPTURING METHOD, IF IT WORKED BEFORE, THEN YOU BROKE THE BUILD. CHECK THE FUCKING LOGS. Don't use Mac screenshot capture, it's always black. You must get a capture from within the process itself.

Do not fallback to WebGL if WebGPU doesn't work, fix WebGPU. Do not run web builds from tmp directory (ie. use `./app/arken_prism_dev/build-web` not `./tmp/something`)

For every turn, return results in a delivery format that reflects a strong production code-development team. Each turn output should reflect the behavior of an architect, implementer, reviewer, verifier, and integrator working together, while remaining concise and execution-focused. Make sure to include a summary of what you accomplished, and a detailed breakdown of what should be done next (from the plan if there is one, or what can be done to take it to the next level).

Check if there's a recent plan in the thread. If there is, continue a large slice from the plan. Don't send me everything single thing you are thinking. Your model often rushes to give incorrect solutions. I do not want you saying you are done unless you've delivered a large slice of USEFUL deliverables. Always ask yourself "Am I contributing the maximum amount of useful work as I can in one turn?" Try to complete as many things as you can in the plan in one turn, it's more efficient.


## Operator Working Rules

- For MCP servers, prefer the default `node` runtime on the machine.
- Use `nvm use 20` only when the specific project/runtime is hardened to Node 20 or when the default Node runtime causes a real compatibility issue.
- Check `.env` for flags that are used to expedite development, if set to `true` then skip those steps and finish the turn early. Flags: SKIP_E2E, SKIP_WEB_BUILD, SKIP_DESKTOP_BUILD

## AXL Output Discipline

- The AXL binary for this machine is available on `PATH` as `axl` and currently resolves to `/Users/dev/.local/bin/axl`.
- For commands that can produce large or noisy local output, prefer the matching AXL command first.
- Only fall back when the requested verb or flags are not supported by AXL, the task requires exact unfiltered output, or the AXL binary is unavailable.
- On fallback, always narrow scope to the smallest useful output.

### AXL Rewrites

- `git status` -> `axl git status`
- `git diff` -> `axl git diff`
- `git log` -> `axl git log`
- `cat <file>` -> `axl read <file>`
- `cat <file>` when exact raw content is required -> `axl read --raw --full <file>`
- `head <file>` -> `axl read --head 10 <file>`
- `head -n N <file>` -> `axl read --head N <file>`
- `tail <file>` -> `axl read --tail 10 <file>`
- `tail -n N <file>` -> `axl read --tail N <file>`
- `sed -n 'A,Bp' <file>` -> `axl read --range A:B <file>`
- `grep <pattern> .` -> `axl grep <pattern> .`
- `rg <pattern>` -> `axl grep <pattern> .`
- `grep/rg` with a pattern that starts with `-` -> `axl grep -e '<pattern>' <path>`
- `grep/rg` with multiple literal patterns or shell-sensitive literals like backticks / `${` -> `axl rg -F -e '<pattern1>' -e '<pattern2>' <path>`
- `find <path> ...` -> `axl find <path> ...`
- `ls` -> `axl ls`
- `pytest` -> `axl pytest`
- `cargo test` -> `axl cargo test`
- `go test` -> `axl go test`
- `tsc` -> `axl tsc`
- `cmake --build <args>` -> `axl cmake build <args>`
- `ctest <args>` -> `axl ctest <args>`
- `docker ps` -> `axl docker ps`
- `docker logs <container>` -> `axl docker logs <container>`
- `kubectl logs <pod>` -> `axl kubectl logs <pod>`
- `node --check <file>` -> `axl node --check <file>`

### Supported Scope

- axl supports only:
- `git status|diff|log`
- `read`
- `grep`
- `find`
- `ls`
- `pytest`
- `cargo test`
- `go test`
- `tsc`
- `cmake build`
- `ctest`
- `docker ps|logs`
- `kubectl logs`
- `node --check`

### Fallback Rules

- Do not assume generic proxying.
- Do not use `axl proxy <command>`.
- If a needed verb is unsupported, use the original command with narrowed scope.
- Prefer scoped fallbacks like:
- `git diff -- <path>`
- `git log -n 20`
- `axl read --range start:end <file>`
- `axl grep -e '<pattern>' <path>` for leading-dash patterns
- `axl rg -F -e '<pattern1>' -e '<pattern2>' <path>` for multiple literal patterns or shell-sensitive literals
- `rg -n <pattern> <path>`
- `find <path> -maxdepth N -type f -name '<glob>' | head -n N`
- `pytest -q <path> -k <expr>`
- `cargo test <name> -- --exact`
- `docker logs --tail 100 <container>`
- `kubectl logs --tail 100 <pod>`

## Memory Discipline

Prefer project memory and MCP brain/spec tools over global Codex memory. Keep the token footprint small.

### Arken Mesh Ownership Safety

- Memory checks must go through the configured MCP tools, normally `codex_connect.search_memory` or `codex_connect.load_memories`. Do not run local `arken search-memory`, `arken load-memories`, `arken daemon`, `arken index`, or direct SQLite/WarpSpeed openers just to satisfy the memory-check rule.
- Never try to claim ownership of a project `.arken/mesh.ws` store from an agent thread. Do not delete `.arken/mesh.lock`, start a competing daemon, run `SpecStore::open*`, run mutating `sqlite3`/PRAGMA commands, or kill the owner process unless the user explicitly asked you to repair Arken Mesh runtime ownership and you have verified the exact PID is not Codex or an unrelated system process.
- If `search_memory`, `load_memories`, or another memory tool fails with text like `database owner already active`, `refusing unsafe local fallback`, `daemon request failed after dispatch`, `failed to connect websocket socket`, or `.arken/mesh.ws`, make exactly one concise note that project memory is unavailable for this turn. Then proceed from current repo files, logs, process state, and archive/search tools that do not touch the project Mesh store.
- For Arken Mesh/Codex Connect runtime repair only, inspect ownership with narrow commands such as `ps`, `lsof`, and reading `.arken/mesh.lock`; restart only the exact stale Mesh daemon or owner process after confirming its command line. Never broad-kill Node, Codex, Dock, or unrelated Arken services.

- Query project memory early only when it is likely to change the plan: recurring repo/domain work, existing plan execution, repeated subsystem bugs, known operator preferences, or ambiguous continuity that prior work probably already resolved.
- For general repo continuity, use the project memory path first: resolve the project, then use `search_memory` with narrow terms. Do not start with broad dumps.
- When the task is about the current system state and the repo may contain obsolete docs/specs, prefer `search_memory` with `historyMode = "exclude"` so historical material does not outrank current authority. Use `historyMode = "only"` when explicitly auditing deprecated or superseded material.
- For crash recovery, new-thread rehydration of a known agent, or an explicit restore request, call `load_memories` instead of trying to reconstruct context from transcript history.
- Keep queries narrow. Prefer concrete terms like repo, subsystem, file path, bug name, command, benchmark, or decision keyword. Ask for compact top hits or a bounded context pack, not full archives.
- When a project doc/spec/manual page is obsolete but still useful as history, do not delete it or silently ignore it. Explicitly retire it with `retire_memory`, passing exact target refs, replacement refs, and a reason so retrieval can degrade it smartly instead of surfacing it as current authority.
- For subagents and skills, pass distilled memory only: 3-7 bullets, exact file paths, or one compact context block. Do not forward raw transcripts or make multiple agents rediscover the same memory.
- Save only durable reusable knowledge: decisions, invariants, runbooks, benchmark findings, bug lessons, ownership rules, and explicit supersessions. Skip ephemeral status, raw logs, drafts, and facts that are obvious from current code.
- If you discover something important about the project, solve an obscure bug, or the user gives a durable instruction that should affect future work, save it while the context is fresh. Prefer `save_memory` by default for durable memory. Use `submit_result` / `submit_update` only when you need explicit routing, richer update metadata, or agent-context targeting.
- Treat direct user guidance about how work must be validated or performed as memory-worthy when it is likely to matter again. Example: if the user says not to suppress a visible effect while verifying whether the fix worked, save that as a durable project instruction instead of relying on transcript recall.
- `save_memory`, `submit_result`, and `submit_update` are plan-first. Do not call backend apply tools directly unless the user explicitly asks, or a reviewed archive/import flow is replaying the planned targets.
- When using `submit_update`, prefer top-level `changedFiles` and `evidence` with absolute paths. If you also pass `targetContexts`, mirror the same absolute anchors inside each target context instead of relying on nested relative paths alone.
- If a memory write does not truly need spec-store or agent-context routing, prefer `save_memory` instead of `submit_update`.
- When saving agent-specific purpose, execution gates, role constraints, or recovery capsules, make the payload explicit enough for `submit_result` / `submit_update` to route it to `agent_context`.
- For recurring clarified spec questions, use `spec_query_memory_record`.
- Put project-specific durable knowledge into the project's own docs/spec/manual memory system. Do not use `~/.codex/memories/core-memory.md` for anything.
- Make one consolidated memory save near the end of the turn, after verification, instead of many small writes during exploration.

### Memory Checkpoint Contract

Use project memory more aggressively, but only at high-leverage moments. The best memory check is after understanding the user request and resolving the project, but before forming the execution plan or editing files.

Mandatory early memory check:
- At the start of any non-trivial repo task, resolve the project and attempt one narrow `search_memory` before planning. If the memory tool reports an Arken Mesh ownership/daemon/unsafe-fallback failure, stop using project-memory tools for that turn and continue from source/logs/runtime evidence instead of trying to force memory to work.
- Always check memory when the request mentions a recurring subsystem, prior bug, benchmark, thread, PR, branch, worktree, capsule, MCP service, daemon, importer, archive, WebGPU/game proof, capture workflow, or user-specific preference.
- Always check memory when the user says or implies continuity: “next”, “again”, “recent”, “same issue”, “that plan”, “yesterday”, “last time”, “restore”, “why did this happen”, “where did I put”, or “we already fixed”.
- Always check memory before continuing an existing plan or before deciding a prior plan is obsolete.
- Always check memory before large multi-file edits, migration work, service/runtime changes, storage/schema changes, or anything likely to take more than 15 minutes.
- Always check memory after a surprising failure if the failure smells like a known project issue, flaky workflow, environment rule, or previously solved bug.

Memory read procedure:
- Prefer project-local memory through the MCP memory/spec tools. Do not start with global Codex memory.
- Use 1-3 narrow queries built from concrete nouns in the request: repo name, subsystem, file path, bug name, command, benchmark, branch, thread title, or error text.
- Use `historyMode = "exclude"` when current authority matters and old docs/specs may be stale.
- Use `historyMode = "only"` only when explicitly auditing deprecated behavior or trying to recover historical context.
- If the query is a crash recovery, thread rehydration, or explicit restore request, use `load_memories` instead of ad hoc `search_memory`.
- If memory returns weak or irrelevant hits, say so briefly and proceed from source/logs/runtime evidence. Do not keep broadening endlessly.
- Treat low-signal memory results as no useful memory, not as context. If an explicit `projectRoot` or `candidateProjectRoots` was provided, results must stay scoped to that project and must contain at least one strong domain anchor from the query, such as the repo name, subsystem, file path, command, bug phrase, or exact error text. Do not use unrelated `token-fallback` or repeated `agent_context` hits that only match generic terms like `test`, `build`, `server`, `mcp`, or `workflow`.
- When only weak/token-fallback hits remain, summarize the outcome as `No relevant project memory found for this query` and continue from current source, logs, database state, tests, captures, or runtime behavior. Do not let irrelevant memories distract the plan.

Memory usage in planning:
- Treat memory as input to the plan, not as proof. Verify against current source, logs, database state, tests, captures, or runtime behavior before relying on it.
- If memory conflicts with current code or live state, prefer current code/live state and note the conflict.
- Distill memory into a few actionable facts. Do not paste raw memory dumps into subagents or final responses.

Memory write timing:
- Near the end of a turn, after verification, save one consolidated durable memory if the work produced reusable knowledge.
- Save decisions, invariants, runbooks, benchmark findings, obscure bug lessons, environment constraints, validation rules, and explicit supersessions.
- Do not save ephemeral status, raw logs, temporary paths, draft plans, or facts obvious from current code.
- If a stale memory/document caused bad retrieval, retire or supersede it instead of letting it remain current.

### Recovery Discipline

When context is likely missing, restore before doing substantive work.

- Use `load_memories` only for:
  - crash recovery
  - new thread rehydrating a known agent
  - an explicit restore request
- Pass the current project context plus:
  - `agentId`
  - `agentRole`
  - `skillIds`
- Do not guess from transcript fragments if those fields are available.
- Wait for the `load_memories` result before doing substantive work.
- Treat the restore output as the authoritative compact context pack for that agent session.
- The restore output is an IEEE-style spec, not a transcript replay. Read the `Mission` and `Execution Gate` sections first.

### Memory Write Shape

When storing durable memory, compress it to the smallest reusable form:

- Context: what area this applies to
- Decision or invariant: the durable fact
- Evidence: command/test/log/file that proved it
- Impact: why future turns should care
- Supersedes: older guidance replaced by this, if any

### Plan Project Memory

When continuing or completing work from a titled plan, treat the exact plan title as the durable memory key. Before continuing the plan, call `search_memory` with `planTitle` when the tool schema supports it, or include the exact plan title in the query when it does not.

When a titled plan is active, the end-of-turn consolidated memory save must update that plan project automatically. Prefer `save_memory` and include `planTitle`, `completedSteps` or `completedFeatures`, `planNotes` or `notes`, and `evidence` with commands, tests, captures, commits, or files that prove the completion.

Plans are allowed to evolve. When current source/runtime evidence changes the plan, include `planSteps` with the revised step list and statuses, or use notes to explicitly say which steps were added, renamed, split, reordered, blocked, skipped, or superseded. Do not preserve obsolete steps as current just because they appeared in the original prompt.

If the turn only made partial progress, still save compact `planNotes` under the same `planTitle` so the next thread can see current state before generic memories. Do not make the user restate this in each plan prompt.

Do not invent a plan title when none is known. If there is no titled plan, save ordinary memory with the normal Context / Decision / Evidence / Impact / Supersedes shape.

## Operator network policy

- The user may block outbound traffic to random IP addresses that do not appear to have legitimate certificates.
- If browser unlock, WebGPU/browser smoke runs, or other network-dependent tests start failing consistently with certificate, TLS, browser-unlock, or suspicious-host errors, report that pattern clearly and notify the user for review.
- Do not preserve backward compatibility unless the user explicitly asks for it.
- When a visible runtime or HUD revision marker exists, bump it on every user-visible change so the user can verify they are on the latest build.

# Shell Project Rules

1. Don't create temporary static servers, use the Node SEO SPA server.

# Code Rules

1. An empty `catch` in JS is never acceptable, you must at least console log.

# UI Rules

1. Before you assume clicks aren't working or something, try interacting with nearby elements to see if they work. Do that to narrow down the issue.

# Arken Project Rules

1. JS is only for E2E, UI or WebGPU games. Do not use JS for web servers, API sidecars, etc. Use CMake and built C++ binaries.
2. Arken SDK is meant to be generic, no hardcoding checks for specific apps there. You can use params/DI to influence behaviour, but if it's not reusable just put it in your capsule/app code.
3. Everything should be broken into capsules and in those laid out architecturally correct.

# Oasis/Sanctuary Rules

1. Your capture proofs should be Unity-vs-WebGPU scene comparison proofs.

## C/C++ Architecture Rules

Use file-size policy:

- Hard fail CI if any hand-written `.mjs`, `.js`, `.jsx`, `.cpp`, `.hpp`, `.h`, `.mm`, or `.m` file exceeds 5,000 lines.
- Warn locally when a hand-written file exceeds 3,000 lines.
- Files anywhere between 200-2000 lines are great.
- Generated files must live under a clearly named `generated/` path and should be excluded from normal AI reasoning unless the task explicitly targets codegen.
- Split tests by behavior or subsystem, not by one giant integration file.

Use C++20 unless a target-specific toolchain forces otherwise. Do not introduce C++23 or compiler-specific language features without updating all supported Arken targets.

Respect capsule boundaries:

- A capsule owns its public API, private implementation, tests, and CMake target dependencies.
- Do not reach into another capsule’s private source tree.
- Do not add global include directories, global compile definitions, or transitive dependencies to “make it build.”
- Public headers should include only what their API needs. Prefer forward declarations when possible.
- Core capsules must not depend on platform, renderer, process, browser, Node, Objective-C, Android, Emscripten, HTTP, WebSocket, or service-launch code.
- Platform code belongs under platform-specific runtime layers, not inside core state, routes, snapshots, or capsule model logic.

Keep platform boundaries explicit:

- Use platform capability structs and runtime interfaces instead of scattered `#ifdef`s.
- Keep Apple, Android, Emscripten, OpenGL, WebGPU, EGL, JNI, Objective-C, and browser bridge code at the platform edge.
- Do not add WebGL fallback when WebGPU is the requirement.
- Android/mac/web behavior should be gated by platform capabilities, not by ad hoc route checks.

Render-loop discipline:

- No blocking I/O, process launch, filesystem scans, network calls, sleeps, or service startup in render paths.
- Render code consumes snapshots/state and emits user actions. It should not own runtime orchestration.
- Avoid heap churn, repeated JSON parsing, repeated string formatting, and repeated filesystem checks per frame.
- Cache stable IDs, labels, and derived layout data when they are reused across frames.
- Keep immediate-mode UI drawing separate from service state transitions.

State and determinism:

- No wall-clock reads in core transition logic. Inject timestamps at boundary commands.
- No randomness in core state transitions. Random IDs/salts belong at client or boundary layers.
- Use deterministic ordered containers when snapshot output, replay, tests, or hashing depends on iteration order.
- Use unordered containers only when order is irrelevant.

Ownership and errors:

- Prefer values, references, and `std::unique_ptr` for ownership.
- Use `std::shared_ptr` only for real shared lifetime across async/task/runtime boundaries.
- Raw pointers are non-owning only.
- No empty `catch` blocks. Log, return an error, or deliberately translate the failure.
- Do not throw exceptions across capsule or platform API boundaries unless that API already uses exceptions.
- Prefer explicit status/result types for recoverable library errors and boundary classification.
- Assertions are for invariants, not user/input/runtime failures.

CMake and build hygiene:

- Every capsule target declares its real dependencies.
- Do not fix dependency issues by linking giant umbrella targets unless that is already the local pattern.
- Avoid target-wide compile definitions when a source-local constant or small config object works.
- Keep generated assets and staged runtime files out of source-controlled hand-written paths.
- Do not create parallel build systems for the same product unless required by the platform.

Testing and validation:

- Add focused capsule tests for core state and route behavior.
- Add route snapshot or smoke tests when UI-visible behavior changes.
- When touching shared SDK/platform code, verify at least the affected product target plus one non-target platform when practical.
- For visual/runtime changes, produce process-native captures, not OS screenshot fallbacks.
- Durable design decisions belong in MCP/spec memory or executable tests/checks, not standalone repo docs.

Syntax:

- Do not add duplicate key names for snake_case and camelCase when doing JS. Just use camelCase for JS. Do a transform step, snake_case <-> camelCase for service calls.

## Guided Proof Videos

For UI/runtime work, produce a guided proof video: a 5s-10s process/WebView capture with a narration band added as new pixels above the app frame. The guide text must not overlay, obscure, resize, or modify the app UI itself.

The guided proof should show what action is being performed at the same time it happens, such as opening a route, clicking a thread, sending a message, waiting for processing, or verifying the final state. Start recording before the user action, not after completion.

Valid guided proofs:
- use the app/process/WebView capture path, not macOS screenshots;
- add the step text in a separate top band above the captured UI;
- reject loading, error, black, or broken frames;
- include the actual transition or processing state being validated;
- save artifacts under the project `.tmp` or build/e2e-artifacts directory.
