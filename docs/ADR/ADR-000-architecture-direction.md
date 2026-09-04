# ADR-000 — Start with a Modular Monolith

Status: Proposed

## Context
New Resume Builder AI is an empty/new repository and currently has no demonstrated scale, independent service teams or deployment boundaries requiring microservices.

## Options
1. Modular monolith backend
2. Multiple microservices from day one

## Decision
Start with a modular monolith with explicit internal module boundaries.

## Rationale
- Lower operational complexity
- Easier local development and testing
- Easier transactional consistency for core resume workflows
- Faster MVP delivery
- Module boundaries preserve a future extraction path if measurements/business boundaries later justify services

## Consequences
Developers must not treat a modular monolith as unstructured code. Identity, Resume, Template, Export, AI Assistance and ATS Analysis require explicit boundaries.

## Revisit When
- independently scaling workloads emerge
- operational isolation becomes necessary
- deployment cadence/team ownership differs materially
- measured reliability/scaling constraints justify extraction

Do not split services solely because microservices are fashionable.
