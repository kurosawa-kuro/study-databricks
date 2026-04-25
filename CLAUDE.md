# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository nature

This is a **personal study-notes repository**, not a code project. There is no build, lint, or test tooling — only Japanese-language Markdown files that lay out a Databricks self-study curriculum. Do not invent build/test commands that do not exist.

All authored content is in Japanese. When editing existing files or adding new notes, match the Japanese voice and terse, bullet-heavy formatting already in use (short headings, `---` separators, occasional fenced `text` blocks with `id=...` used as visual callouts).

## Content architecture

All notes live under `docs/`. The two existing files represent a **2-phase learning plan** with a specific framing that should be preserved if new notes are added:

- `docs/学習ロードマップ.md` — top-level 2-phase plan and 7-day schedule:
  - Phase ①: *Databricks 基礎機能編 (Lakehouse コア)* — Workspace/Compute 使い分け, Delta Lake, Medallion, Workflows + **DLT**, Unity Catalog
  - Phase ②: *Databricks 固有のML運用編* — MLflow 統合, Model Registry (UC 配下), Feature Store, Batch 推論, 監視
- `docs/チュートリアル_Day1-4.md` — Day 1–4 expansion of Phase ① (基礎機能編 + Delta/Workflow(+DLT)/Unity Catalog編), code-free and written for a reader who already knows cloud infra, DWH, pipelines, and **汎用ML (LightGBM / XGBoost / scikit-learn / MLflow 基本)**.

The reader's ML coding skills are assumed — the plan **explicitly excludes 汎用ML学習コード** and focuses on Databricks-specific features only. Phase ② is *not* generic ML/MLOps; it is scoped to Databricks-specific ML operations (資産管理・運用フロー) and is designed to enable a Vertex AI vs. Databricks adoption judgment.

The intended continuation (not yet written) is **Day 5–7: Databricks 固有のML運用 編** covering MLflow 統合, Model Registry, Feature Store, Batch 推論, 監視. New files should slot into this sequence under `docs/` with Japanese filenames matching the existing convention (e.g. `チュートリアル_Day5-7.md`).

## Editorial stance baked into the notes

The author is explicitly *not* a beginner and has written these notes to avoid:

- UI memorization
- Spark deep-dives
- Certification rote learning
- Aimless notebook exploration

The stated goal is being able to judge *"whether to adopt Databricks on a GCP engagement"*, not to "have touched Databricks." Preserve this framing — suggestions that drift toward beginner Spark tutorials or certification prep contradict the author's explicit intent.

The recurring comparison axis is **Databricks vs. GCP / BigQuery / Vertex AI**. Keep this comparison lens when extending notes.
