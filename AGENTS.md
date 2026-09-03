# Arc VCC 2026 Project Instructions

## Scope

This repository develops a staged research prototype for the Arc Virtual Cell Challenge 2026. The working hypothesis is that a target context's NTC distribution supplies the initial state while perturbation data from external contexts constrain a shared, testable response mechanism. Treat that statement as a hypothesis, not a conclusion.

Data discovery and acquisition are upstream work. R00 method/reuse research may run before data arrives; data engineering starts only after the user manually copies the approved acquisition bundle into `Stage_0-raw_training_data/`.

## Recursive Project Scope

- This root `AGENTS.md` governs every active descendant directory, including all `stages/**`, regardless of the agent's starting working directory.
- An agent starting below the project root must first locate this root by walking ancestors, then read the root `.doc/AI_progress.md` and `.doc/AI_plan.md` before reading stage documents.
- Every direct stage directory must expose this exact root file through `AGENTS.md -> ../../AGENTS.md`; this makes the same authoritative content load even when a non-Git stage directory is used as the working root.
- A direct symlink to this root file is the only permitted active nested instruction under `stages/**`. Do not create `AGENTS.override.md`, deeper `AGENTS.md`, or stage-specific policy text; if one appears or a symlink drifts, stop and resolve the scope conflict before work continues.
- `codex_env_trans/AGENTS.md` is a preserved migration-bundle artifact and is not an instruction source for work outside `codex_env_trans/`.
- Resolve every relative project path in this file from `/media/sshuser/ExtData2/data/Arc_AIVC_2026`, even when the file was loaded through a stage symlink.

## Required Read Order

1. Read [`.doc/AI_progress.md`](.doc/AI_progress.md) to identify the current stage and module.
2. Read [`.doc/AI_plan.md`](.doc/AI_plan.md) for stage routing and approval gates.
3. For the active stage, read its `.doc/design.md`, `.doc/AI_plan.md`, and `.doc/AI_progress.md` completely before acting.
4. Before method planning or implementation, read [`参考资料库/rules.md`](参考资料库/rules.md), complete the active stage's `T00` live research/reuse gate, then read every stage-linked scientific reference.
5. Use `技术路线与项目设计.md` for the scientific rationale and `技术路线与项目设计-v2.md` as a reviewed design input, not as the active execution plan.
6. Reconcile every progress claim with current files or artifacts before changing state.

When official competition rules, the local competition note, and an older design disagree, current official first-party rules control the competition contract; preserve the disagreement in a report rather than silently rewriting history.

## Documentation Ownership

- Root `.doc/AI_plan.md` and `.doc/AI_progress.md` only route between stages. Do not copy module task details into them.
- Each `stages/<stage>/` directory owns its detailed design, implementation contract, progress, derived artifacts, runs, and reports.
- `.doc/design.md` is human-facing and explains the scientific question, design, workflow, and interpretation boundaries.
- `.doc/AI_plan.md` is a prospective engineering contract. It must not contain current status, observed metrics, run IDs, timestamps, or recovery points.
- `.doc/AI_progress.md` is the only stage document that records execution state, validation results, blockers, and the recovery point.
- `LOG.md` is the root chronological operation record when a task is substantial enough to need one.

Designs, plans, progress records, and human-review reports are written in Chinese. Normalized schemas, machine manifests, logs, code, code comments, and inter-agent handoffs are written in English.

## Execution Control

- Defense level: `L3` for research and algorithm-prototype work.
- Execution policy: `phase_boundary`.
- Record every small-task checkpoint in the active module progress file. Continue within an already approved phase; stop at every declared phase review gate.
- A stage may advance only after direct artifact review is recorded in its progress file and the user approves the stage boundary.
- If subagents are explicitly authorized, the coordinator alone edits root and module `AI_plan.md`/`AI_progress.md`; workers write scoped evidence and return handoffs.
- `agentmemory` and `autoresearch` require explicit current-turn authorization. Autoresearch additionally requires a metric, baseline command, per-trial budget, total ceiling, stopping rule, and rollback rule.

## Method Research And Reuse Gate

- Apply [`参考资料库/rules.md`](参考资料库/rules.md) before creating or selecting any data-processing method, visualization method, statistical estimator, metric, baseline, GRN method, model, solver, decoder, calibration, or submission implementation.
- R00 establishes the reusable baseline; it never waives the active stage's real-time `T00` survey of current project assets, installed dependencies, primary literature, official repositories, versions, licenses, and task fit.
- Prefer, in order when scientifically compatible: an existing project implementation, standard/native capability, an installed mature dependency, a reference-library registered official implementation through a narrow adapter, then a newly verified mature implementation. Use `minimal_new` only with documented incompatibility evidence and the required approval.
- Before `T01+`, the stage must own `reports/method_reuse_review.md` and `reports/reuse_decision.yaml` with `reuse_gate: pass`. Any material scope change reopens the gate.
- Archive and index every newly shortlisted or used paper/method before it influences design or implementation. A search hit rejected before shortlisting may remain only in the stage research report with its rejection reason.

## Data Contract

- Treat `Stage_0-raw_training_data/` as an immutable upstream asset. Read it in place; do not rename, normalize, decompress over, or repair raw files destructively.
- Verify the user-copied bundle against its supplied manifests. Checksums are required here because copy integrity and dataset provenance are part of the task.
- Write canonicalized, QC, normalized, visualization, and model-ready products only under stage-owned `derived/` paths with explicit release IDs.
- Preserve raw integer counts separately from normalized/log-transformed analysis layers.
- Preserve dataset, study, context/cell model, batch, replicate, time, perturbation modality, target, guide, NTC, and QC provenance. Missing metadata stays missing; never infer a value silently.
- CRISPRi is the main training modality. KO, CRISPRa, drug-stimulated, multi-guide, and otherwise incompatible records remain separately labeled and enter only a predeclared auxiliary or evaluation branch.
- No official validation/test perturbation response may enter fitting, feature selection, graph selection, calibration, blending, or manual correction.

## Scientific Evidence Rules

- Separate `workflow_verdict` from `scientific_verdict` in every gate report.
- Scientific statuses are `not_tested`, `unresolved`, `weakened`, `supported`, or `falsified`, and must name the exact hypothesis or sub-claim.
- A successful file check, test, scorer run, UMAP, or visual review is not scientific support by itself.
- A failure report must classify plausible causes as `supported`, `ruled_out`, or `unresolved`, cite evidence, state the strongest permitted conclusion, and list prohibited conclusions.
- Use `falsified` only after the registered estimand was evaluated and implementation, numerical/protocol, data, and geometry alternatives were excluded.
- UMAP and human visual review are diagnostic. Freeze review questions before inspection, record accept/quarantine/reprocess decisions with reasons, and never hand-edit predictions or select post-hoc candidates from hidden responses.

## Engineering Rules

- Prefer the smallest implementation that satisfies the active module contract. Search for an existing helper or installed dependency before adding one.
- Put reusable code under `src/arc_vcc/`; keep stage scripts thin and stage outputs under the owning `stages/<stage>/` directory.
- Configuration, random seeds, fold definitions, gene order, target panels, environment versions, and model identifiers must be explicit and serializable.
- Use sparse or backed/chunked access for large matrices. Do not materialize an entire multi-dataset corpus when a chunked pass suffices.
- At each heavy-stage boundary, release large objects, call `gc.collect()`, and when CUDA is active call `torch.cuda.empty_cache()` and `torch.cuda.ipc_collect()` when safe.
- L3 testing requires schema/shape/range sanity checks, property tests for core numerical logic, leakage tests for fold-scoped fitting, and small synthetic integration tests. Do not pursue blanket coverage or adversarial-input defenses absent a concrete risk.
- Long jobs require a dry run, resource estimate, explicit output path, periodic progress output, and a resumable checkpoint. Hand off execution when runtime or inaccessible hardware makes local verification impractical.

## Competition Delivery Rules

- Pin and record the official bundle, panel, gene order, scorer/CLI versions, and submission schema for each partition.
- Analysis matrices may be normalized; competition output must satisfy the current official raw-count contract and be generated by the declared model pipeline.
- Run the official local dry-run validator before any external submission. External submission is a user approval gate.
- The final test workflow replays the frozen validation pipeline with only the official partition inputs changed. Do not create a separate test-only modeling path.

## Protected References

Preserve these source documents unless the user explicitly requests a revision:

- `技术路线与项目设计.md`
- `技术路线与项目设计-v2.md`
- `document/比赛任务.txt`
- `document/已有想法.txt`
- `参考资料库/`
- `参考资料库/rules.md` is the authoritative reuse-governance contract; amend it only with the corresponding plan/progress change.
- `codex_env_trans/`

Rules-authorized additive method cards, indexes, and provenance updates are allowed; preserve existing evidence and do not overwrite historical source snapshots.

<!-- File structure: project scope, read order, record ownership, execution control, data/science/engineering contracts, delivery rules, and protected references. -->
