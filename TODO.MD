# AgenC Coding-First Shell TODO

> Active execution roadmap for making AgenC feel first-class for coding without
> collapsing the product into a code-only assistant.
>
> Status: compared against the current AgenC workspace and a live local
> `../claude_code` checkout on 2026-04-12.
>
> Execution status as of 2026-04-13: Phases 0 through 9 are implemented in
> `agenc-core`. This file is now a reconciled shipped-roadmap record plus a
> short post-roadmap backlog.
>
> Scope: this roadmap covers the shell, operator UX, runtime integration, and
> rollout plan needed to turn AgenC's existing runtime into a strong coding
> product while preserving general-purpose agent workflows.

## Executive Goal

- [x] Make AgenC excellent for day-to-day coding in a repo.
- [x] Keep one daemon/gateway/runtime authority for coding, research, operator,
      validation, documentation, and general task execution.
- [x] Preserve multi-agent and general-purpose workflows instead of shipping a
      code-only fork of the product.
- [x] Reuse existing runtime primitives where they already exist instead of
      rebuilding them behind a new stack.

## Positioning

The target is not "copy Claude Code feature-for-feature."

The target is:

- [x] AgenC should have a coding-first shell mode that feels as deliberate and
      ergonomic as Claude Code.
- [x] That shell should sit on top of the same session, delegation, approval,
      task, worktree, tool, plugin, MCP, memory, and watch runtime that powers
      the rest of AgenC.
- [x] Coding should become one strong mode of AgenC, not a separate product
      that bypasses the runtime.

## Ground Truth From The Audit

### What AgenC already has

- [x] Real file tools:
      `system.readFile`, `system.writeFile`, `system.appendFile`,
      `system.editFile`, `system.listDir`, `system.stat`, `system.mkdir`,
      `system.delete`, `system.move`.
- [x] Real shell execution:
      `system.bash`.
- [x] Real task tracking:
      `task.create`, `task.list`, `task.get`, `task.update`, `task.wait`,
      `task.output`.
- [x] Real internal worktree isolation for delegated workers.
- [x] Real approvals and policy gating.
- [x] Real session infrastructure, remote session infrastructure, and
      resumability primitives.
- [x] Real MCP client infrastructure.
- [x] Real watch/subagent/planner observability surfaces.
- [x] Real skills and plugin lifecycle surfaces.

### What the later phases shipped

- [x] First-class shell UX for MCP, plugins, and skills that is coherent with
      the coding shell/session/workflow surface.
- [x] A watch cockpit for repo state, reviews, verification, and child-agent
      ownership.
- [x] Explicit shell-facing multi-agent orchestration instead of mostly hidden
      runtime-driven delegation.
- [x] Rollout, gating, and broader integration validation on the existing
      autonomy plane.

### What remains after the roadmap

- [ ] Broader end-to-end smoke coverage that exercises shell, watch cockpit,
      MCP controls, and multi-agent flows together in one scenario suite.
- [ ] Additional user-facing docs that explain cross-profile versus
      coding-focused commands in one short operator guide.
- [ ] Optional future overlay work where `research`, `validation`, and
      `documentation` can also act as lightweight task overlays on top of the
      explicit profile system.

### What AgenC has, but has not yet unified for coding

- [x] Several coding-relevant surfaces already exist in fragmented form across
      runtime commands, watch, task tracking, approvals, sessions, and internal
      worktree flows.
- [x] The roadmap should productize and unify those surfaces before it invents
      parallel replacements.

### What remains missing for coding specifically

- [x] A named coding shell mode and product contract.
- [x] A first-class coding command surface.
- [x] A first-class code search and code intelligence layer.
- [x] A first-class git workflow layer.
- [x] Shell-native worktree UX.
- [x] Shell-native plan/review/diff UX.
- [x] Shell-native MCP/operator UX for coding sessions.
- [x] A coherent way to switch between coding and general-purpose agent work
      without losing the shared runtime.

## Non-Negotiable Product Rules

- [x] Do not create a second authority beside the daemon/gateway.
- [x] Do not fork the runtime into "coding AgenC" and "general AgenC."
- [x] Do not collapse marketplace, research, operator, and general-purpose
      agent flows into coding-specific abstractions.
- [x] Do not reintroduce direct one-off shell state that bypasses runtime
      sessions, approvals, task tracking, or delegation policy.
- [x] Do not make coding-specific assumptions the default for every request.
- [x] Do not degrade the existing subagent, workflow, remote, or watch
      architecture to chase shell ergonomics.

## Product Thesis

- [x] AgenC should expose explicit runtime modes:
      `coding`, `research`, `validation`, `documentation`, `operator`,
      `general`.
- [x] Modes should change tool bundles, prompts, command affordances, watch
      presentation, and default delegation policy.
- [x] Modes should not create separate runtimes.
- [x] Multi-agent workflows should remain first-class in all modes, especially
      `coding` and `general`.
- [x] The same session should be able to host coding work, research work,
      validation work, and operator work when appropriate.

## North-Star Experience

- [x] A user can start `agenc shell coding` in a repo and immediately get a
      coding-first session with repo-aware tools, search, git affordances,
      explicit planning, worktree isolation, and review workflows.
- [x] A user can switch that same session or child sessions into
      `research` or `general` mode without leaving the product.
- [x] A user can delegate different parts of a task to specialized agents:
      coding, docs, research, verification, operator, marketplace, or remote.
- [x] The watch surface can show planner DAGs, child sessions, worktrees,
      diffs, approvals, validation state, and final outputs in one place.
- [x] A user can resume a coding session later and recover repo state, tasks,
      worktrees, outputs, approvals, and subagent lineage.

## Architecture Direction

### Shell placement

- [x] Put the coding-first shell in `agenc-core`, not the umbrella root.
- [x] Keep the root repo limited to roadmap/docs/examples/routing.
- [x] Treat the shell as a runtime client over the existing daemon/session
      model.

### Core abstraction

- [x] Introduce a first-class `shell profile` or `session mode` abstraction.
- [x] Shell profiles should define:
      - [x] allowed/default tool families
      - [x] prompt contract
      - [x] command surface
      - [x] delegation policy defaults
      - [x] watch presentation defaults
      - [x] approval/policy presets
      - [x] resume metadata

### Profile precedence contract

- [x] Define explicit precedence so shell profiles do not fork runtime behavior:
      - [x] hard runtime safety, workspace, approval, and economics controls
      - [x] existing delegation/subagent policy and trusted-root constraints
      - [x] shell-profile defaults
      - [x] session-level user overrides
      - [x] per-child delegated allowlists and execution envelopes
- [x] Define profile persistence as persisted session identity rather than a
      transient presentation preset.
- [x] Add schema-versioning and backward-compat rules for persisted profile
      metadata.
- [x] Migrate by composition over existing runtime controls, not replacement.

### Coding shell is a profile, not a fork

- [x] `coding` becomes one shell profile.
- [x] `general` remains the baseline profile for broad agent work.
- [x] `research`, `validation`, and `documentation` are supported explicit
      shell profiles; overlay-style task presets remain optional future work.
- [x] Child sessions/subagents can inherit or override profile based on the
      delegated job.

## Impact Map

### Product contract and packaging

- `agenc-core/docs/architecture/product-contract.md`
- `agenc-core/docs/architecture/runtime-layers.md`
- `agenc-core/README.md`
- `agenc-core/packages/agenc/`
- `agenc-core/packages/agenc/lib/cli.js`
- `agenc-core/packages/agenc/lib/runtime-manager.js`

### Runtime shell and CLI surfaces

- `agenc-core/runtime/src/bin/`
- `agenc-core/runtime/src/cli/`
- `agenc-core/runtime/src/gateway/`
- `agenc-core/runtime/src/watch/`
- `agenc-core/runtime/src/workflow/`
- `agenc-core/runtime/src/tools/`
- `agenc-core/runtime/src/mcp-client/`
- `agenc-core/runtime/src/skills/`
- `agenc-core/runtime/src/plugins/`

### Coding-specific runtime additions

- `agenc-core/runtime/src/tools/system/`
- `agenc-core/runtime/src/gateway/worktree-isolation.ts`
- `agenc-core/runtime/src/gateway/subagent-infrastructure.ts`
- `agenc-core/runtime/src/gateway/tool-handler-factory*.ts`
- `agenc-core/runtime/src/gateway/system-prompt-builder.ts`
- `agenc-core/runtime/src/cli/sessions.ts`

### Watch and operator UX

- `agenc-core/runtime/src/watch/agenc-watch-app.mjs`
- `agenc-core/runtime/src/watch/agenc-watch-planner*.mjs`
- `agenc-core/runtime/src/watch/agenc-watch-subagents.mjs`
- `agenc-core/runtime/src/watch/agenc-watch-diff-render.mjs`
- `agenc-core/runtime/src/watch/agenc-watch-workspace-index*.mjs`

### Root docs

- [x] `TODO.MD`
- [x] root docs that describe product positioning after the shell lands
      (audited on 2026-04-12; main public shell/session docs were already
      aligned)

## Phase 0: Product Contract

### Goal

- [x] Decide and document what "coding-first shell" means in AgenC terms.

### Deliverables

- [x] Write a short architecture note in `agenc-core/docs/architecture/`
      defining the shell as a runtime client and profile system.
- [x] Define the supported shell profiles:
      `coding`, `general`, `research`, `validation`, `documentation`,
      `operator`.
- [x] Define what is shared across profiles:
      sessions, approvals, tasks, subagents, worktrees, watch, MCP, plugins,
      memory, remote.
- [x] Define what is profile-specific:
      tool bundles, command bundles, prompts, status UI, defaults.

### Exit criteria

- [x] No ambiguity remains about whether coding is a new product or a mode on
      the current product.

## Phase 1: Shell Profile Infrastructure

### Goal

- [x] Add a runtime-level shell profile system so coding can be first-class
      without special-casing every layer.

### TODO

- [x] Explicitly inventory what already exists in daemon commands and watch so
      Phase 3 reuses and consolidates before adding new shell-only features.
- [x] Add a `session mode` or `shell profile` field to session/runtime metadata.
- [x] Thread profile selection through CLI, daemon session creation, remote
      sessions, and resume state.
- [x] Teach the prompt builder to inject profile-specific operating rules.
- [x] Teach tool registry assembly to choose profile-aware defaults.
- [x] Teach approvals/policy defaults to resolve from profile.
- [x] Persist profile in resumable session state.
- [x] Preserve compatibility with existing delegated-child `allowedTools`,
      trusted workspace roots, and execution-location enforcement.

### Must preserve

- [x] Existing non-coding sessions must still work with `general` as the safe
      default.
- [x] Subagents must be able to inherit or override profiles.
- [x] Remote and background-run flows must carry profile metadata cleanly.

## Phase 2: Coding Tool Surface

### Goal

- [x] Expose a deliberate coding tool bundle on top of the existing runtime
      tools.

### TODO

- [x] Keep existing filesystem and bash primitives as the base.
- [x] Add first-class search tools suitable for coding workflows:
      grep, glob, symbol/file search, and repo inventory.
- [x] Add first-class git tools:
      status, diff, show, branch info, staged/unstaged summary, worktree ops.
- [x] Add first-class patch/apply workflow support instead of relying on raw
      shell calls for everything.
- [x] Add first-class "read before edit" and "safe edit" ergonomics to the
      coding bundle rather than leaving them implicit.
- [x] Decide whether LSP/code-index support is native, plugin-backed, MCP-backed,
      or hybrid.
- [x] Add a coding-oriented tool search/discovery surface so the model knows
      what it can use.
- [x] Add profile-aware tool advertisement, ranking, and deferred discovery so
      the coding bundle fits within live model/tool-count limits.
- [x] Add an escape hatch for mixed-mode turns when coding sessions need
      browser, research, remote, or sandbox tools that are not in the default
      advertised bundle.

### Must preserve

- [x] Research/browser/remote/sandbox tools still need to be reachable when the
      task genuinely requires them.
- [x] Coding should not block mixed-mode work like docs, browser testing, or
      remote debugging.

## Phase 3: Coding Command Surface

### Goal

- [x] Give users an explicit operator shell for coding workflows.

### Minimum command set

- [x] `agenc shell coding`
- [x] `agenc shell general`
- [x] `agenc plan`
- [x] `agenc tasks`
- [x] `agenc files`
- [x] `agenc grep`
- [x] `agenc git status`
- [x] `agenc git diff`
- [x] `agenc branch`
- [x] `agenc worktree`
- [x] `agenc session`
- [x] `agenc resume`
- [x] `agenc diff`
- [x] `agenc review`
- [x] `agenc permissions`
- [x] `agenc mcp`
- [x] `agenc plugin` (direct plugin catalog/admin CLI) plus `/plugin` in-shell
- [x] `agenc skills`
- [x] `agenc doctor`
- [x] `agenc status`
- [x] `agenc model`
- [x] `agenc effort`

### TODO

- [x] Decide whether `agenc shell coding` is:
      - [x] the future default entrypoint for repo-local coding sessions via
            the shell-first `agenc` launcher
- [x] Treat packaging and wrapper help text as first-class deliverables for all
      user-facing command additions.
- [x] Define which commands are cross-profile.
- [x] Define which commands are coding-profile only.
- [x] Make command discovery obvious from the shell itself.
- [x] Keep command naming consistent with the existing AgenC CLI style.

## Phase 4: Plan, Review, and Worktree UX

### Goal

- [x] Turn existing planner/subagent/worktree infrastructure into a visible
      coding workflow.

### TODO

- [x] Add explicit plan mode entry and exit for coding sessions.
- [x] Show plan state in watch and shell status.
- [x] Add user-visible worktree mode over the existing internal worktree
      isolation layer.
- [x] Surface which child session owns which worktree and task.
- [x] Make review and verification a first-class phase, not an ad hoc prompt.
- [x] Expose repo diffs and changed-file summaries as shell affordances.
- [x] Allow verification-only or reviewer subagents with restricted tool sets.

### Must preserve

- [x] General-purpose agents still need delegation DAGs, not just coding plans.
- [x] Worktree support must remain optional and profile-aware.

## Phase 5: Session Continuity

### Goal

- [x] Make coding sessions resumable and inspectable at the same quality level
      users expect from dedicated coding products.

### TODO

- [x] Add session listing that is meaningful for coding work:
      repo root, branch, profile, active tasks, worktrees, child sessions,
      last output, pending approvals.
- [x] Add resume flows that can recover coding context cleanly.
- [x] Add branching/forking flows for alternative implementations.
- [x] Add diff/history inspection for session turns and outputs.
- [x] Persist enough runtime contract state to safely recover after disconnects.

### Must preserve

- [x] The same continuity model should work for non-coding long-running work,
      remote sessions, and background runs.

## Phase 6: MCP, Plugins, and Skills UX

### Goal

- [x] Productize the existing MCP and extension infrastructure for shell users.

### TODO

- [x] Add a first-class MCP command surface:
      list servers, inspect tools, enable/disable, reconnect, validate policy,
      show trust/supply-chain state.
- [x] Add coding-relevant MCP discovery and annotations.
- [x] Make plugins and skills discoverable from the shell, not just installable.
- [x] Distinguish clearly between:
      - [x] coding shell tools
      - [x] MCP-provided tools
      - [x] plugin-provided tools
      - [x] marketplace skills
- [x] Keep `tools.*` and `market.*` distinct in both wording and UX.
- [x] Treat MCP and plugin management as privileged control-plane workflows,
      not just shell convenience commands.
- [x] Thread existing MCP governance requirements into the roadmap:
      trust tiers, pinned packages, image digests, binary integrity, catalog
      integrity, and approval coupling.
- [x] Require security and routing review before broadening MCP shell control
      flows.

### Must preserve

- [x] The coding shell cannot become the only consumer of MCP/plugins/skills.
- [x] General-purpose agents should benefit from the same extension surfaces.

## Phase 7: Watch As The Shared Cockpit

### Goal

- [x] Make watch the live control plane for coding and general-purpose work.

### TODO

- [x] Add coding-specific panels to watch:
      repo state, changed files, worktrees, review queue, verification status,
      approvals, branch context.
- [x] Keep existing planner/subagent DAG visibility.
- [x] Show child session profile, owner task, and execution location.
- [x] Show a clean line between shell events, tool events, approvals, and
      background-run events.
- [x] Make watch useful for both a solo coding session and a general multi-agent
      orchestration session.

## Phase 8: Multi-Agent By Default, Not As An Afterthought

### Goal

- [x] Ensure the coding shell reinforces AgenC's multi-agent strengths instead
      of hiding them.

### TODO

- [x] Support explicit child-agent roles:
      coding, docs, research, verification, operator, marketplace,
      browser-testing, remote-debugging.
- [x] Allow the parent session to assign profile + tool bundle + workspace or
      worktree to each child.
- [x] Keep task tracking tied to delegated work and outputs.
- [x] Expose team/workflow/subagent flows in the shell instead of forcing them
      through hidden runtime behavior.
- [x] Preserve evidence-aware delegation so research and verification agents are
      not forced into coding-only tooling.

### Non-negotiable

- [x] A user asking for "general-purpose agents doing different things" must be
      a first-class happy path of the shell design.

## Phase 9: Rollout Strategy

### Goal

- [x] Ship this without destabilizing the broader runtime.

### TODO

- [x] Start behind feature flags or profile gating.
- [x] Ship the shell profile infrastructure first.
- [x] Ship coding commands on top of existing primitives before inventing new
      deep subsystems.
- [x] Keep the first release repo-local and terminal-first.
- [x] Expand to richer watch/web UX only after the shell contract is stable.
- [x] Gate stronger autonomy and multi-agent defaults behind the existing
      rollout controls.
- [x] Reuse the existing autonomy rollout contract:
      SLO gates, canary cohorts, schema-compat migration, rollback drills, and
      positive delegation benchmarks before broad enablement.

## Validation Plan

- [x] Product-contract docs updated in `agenc-core`.
- [x] CLI tests for new shell commands.
- [x] Runtime tests for profile-aware tool assembly and session persistence.
- [x] Worktree tests for child-session lifecycle and cleanup.
- [x] Task-tracker tests for delegated coding flows.
- [x] Approval/policy tests for coding-profile mutating actions.
- [x] MCP policy tests for shell-facing server management.
- [x] Watch snapshot/integration tests for coding panels and multi-agent DAGs.
- [x] Cross-repo smoke tests for `packages/agenc` installer/launcher behavior.

## Rollback Plan

- [x] Shell profiles can be disabled while preserving the existing daemon.
- [x] Coding commands can be hidden behind flags without removing the shared
      runtime primitives.
- [x] Watch additions should degrade gracefully back to current views.
- [x] Worktree UX can fall back to current internal-only isolation if the
      first-class shell layer regresses.
- [x] MCP/product UX changes should not break underlying MCP connectivity.

## Definition Of Done

- [x] AgenC can start a coding-first repo session that feels coherent and
      deliberate.
- [x] Users can still run general-purpose and mixed-mode agent workflows on the
      same runtime.
- [x] Multi-agent delegation is easier to use, not harder.
- [x] Sessions, worktrees, approvals, tasks, diffs, and resume flows all feel
      like one product surface.
- [x] Coding shell UX is built mostly by composing existing runtime strengths
      rather than bypassing them.

## Roadmap Outcome

- [x] The numbered coding-shell roadmap is complete in `agenc-core`.
- [x] Remaining work is limited to the post-roadmap backlog above, not
      unfinished Phase 6 through Phase 9 items.

## Related

- [x] Umbrella ownership and repo routing remain in [AGENTS.md](./AGENTS.md) and
      `docs/`.
