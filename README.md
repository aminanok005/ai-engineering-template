AI Engineering Template

A documentation-driven software engineering template for AI-assisted development.

Designed for Cline and other AI coding agents.

Philosophy

AI should not be asked to build an entire production system from a single prompt.

Instead:

Product Requirements
        ↓
Engineering Blueprint
        ↓
Feature Specifications
        ↓
AI Implementation
        ↓
Automated Tests
        ↓
Review
        ↓
Knowledge Update


The goal is to make AI-assisted development predictable, reviewable, and maintainable.

Repository Structure
01-product/
    Product requirements

02-design/
    System architecture and contracts

03-engineering/
    Security, testing, and coding conventions

04-ai/
    AI workflow and architecture decisions

features/
    Independently implementable features

prompts/
    Reusable AI workflow prompts

AGENTS.md
    Global AI engineering rules

Source of Truth
Concern	File
Product	01-product/PRD.md
Architecture	02-design/ARCHITECTURE.md
Domain	02-design/DOMAIN.md
Data	02-design/DATA_MODEL.md
API	02-design/API.md
Security	03-engineering/SECURITY.md
Testing	03-engineering/TESTING.md
Conventions	03-engineering/CONVENTIONS.md
AI workflow	04-ai/WORKFLOW.md
Decisions	04-ai/DECISIONS.md
Features	features/*.md
AI rules	AGENTS.md
Workflow
1. Product

Fill in:

01-product/PRD.md

2. Bootstrap

Ask the AI agent to execute:

prompts/01-bootstrap.md


The agent analyzes the project and establishes the documentation foundation.

No application features should be implemented at this stage.

3. Design

Execute:

prompts/02-design.md


This creates the engineering blueprint.

Review the result before implementation.

4. Feature Breakdown

Execute:

prompts/03-feature-breakdown.md


The product is decomposed into independently implementable features.

Example:

features/
├── 001-authentication.md
├── 002-organization.md
├── 003-members.md
└── 004-billing.md

5. Implementation

For each feature:

prompts/04-implement-feature.md


The AI agent should:

Understand

Plan

Implement

Test

Validate

Review

Update documentation

Gates

The workflow intentionally contains approval gates:

PRD
 ↓
[Human Review]
 ↓
Architecture
 ↓
[Human Review]
 ↓
Feature Breakdown
 ↓
[Human Review]
 ↓
Feature Implementation
 ↓
Tests
 ↓
Review


This prevents an AI agent from turning ambiguous requirements into a large codebase without review.

Feature Lifecycle

Features use:

draft
 ↓
ready
 ↓
implementing
 ↓
review
 ↓
completed


A feature can also become:

blocked


when required information or dependencies are missing.

Architecture Decisions

Important decisions should be recorded in:

04-ai/DECISIONS.md


This prevents future AI sessions from repeatedly reconsidering decisions that have already been made.

AI Rules

The most important rule is:

The AI agent is an implementation engine operating under documented engineering constraints, not an autonomous architect.

Architectural changes should be explicit and reviewed.

Using With Cline

Open the project in Cline.

Start with:

Read AGENTS.md.

Then execute the workflow described in
prompts/01-bootstrap.md.

Do not implement application features.
Stop when the bootstrap phase is complete.


After review:

Execute prompts/02-design.md.

Do not implement application features.
Stop for human review.


Then:

Execute prompts/03-feature-breakdown.md.

Do not implement features.
Stop for human review.


Finally:

Implement features/001-<name>.md
using prompts/04-implement-feature.md.

Design Principle

Keep project-specific knowledge in project documentation.

Keep AI behavior in AGENTS.md and 04-ai/.

Keep reusable workflows in prompts/.

Keep feature requirements in features/.

This separation prevents the AI instructions from becoming a second undocumented requirements system.