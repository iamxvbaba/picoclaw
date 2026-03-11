---
name: mowang-app-product-manager
description: Product management guidance specialized for the Mowang App (默往 App). Use when requests involve Mowang or the life Android app, including feature analysis, product strategy, PRD writing, module understanding, flow design, roadmap planning, growth ideas, route or entry analysis, and decisions tied to IM, contacts, explore, wallet, album, RTC, login, settings, infrastructure, or deep links.
---

# Mowang App Product Manager

## Overview

Use the Mowang App RAG documents as the primary product knowledge base before making recommendations.
Ground all proposals in the existing product structure, module boundaries, route inventory, and runtime constraints documented under `mowangrag/`.

## Core Rule

Do not treat Mowang App as a blank-slate consumer app.
Assume proposals must fit the current product shape unless the user explicitly asks for a rewrite-level redesign.

## Retrieval Order

1. Read `mowangrag/README.md` for the product summary and document map.
2. Read `references/mowang-rag-map.md` for task-to-doc routing.
3. Read the domain doc that matches the request.
4. If the question spans multiple domains, read all relevant domain docs before answering.

## Domain Routing

- Project structure, flavors, module ownership:
  Read `mowangrag/01-project-overview.md`.
- Startup chain, privacy gate, splash, lifecycle, main runtime:
  Read `mowangrag/02-startup-runtime.md`.
- Login, registration completion, user center, settings, security:
  Read `mowangrag/03-login-user-settings.md`.
- IM, contacts, groups, share backflow, circle, explore:
  Read `mowangrag/04-im-social.md`.
- Wallet, VIP, pretty number, Mowang coin, album, RTC:
  Read `mowangrag/05-wallet-album-rtc.md`.
- HTTP, socket, DB, push, ads, third-party SDK:
  Read `mowangrag/06-data-network-infra.md`.
- ARouter entries, manifest routes, deep links, external entry points:
  Read `mowangrag/07-route-entry-inventory.md`.

## Working Method

1. Identify which Mowang domain the request touches.
2. Read the matching RAG docs first.
3. Summarize the current product reality before proposing change.
4. Then output recommendation, impact, risk, and validation plan.

## Output Requirements

Always distinguish:

- Current state in Mowang App
- Assumptions
- Recommended change
- Affected modules or routes
- Product risk and validation plan

## Use This Skill For

- "分析默往的 IM/探索/钱包模块应该怎么改"
- "给默往 App 写功能 PRD"
- "默往的登录到主流程有什么优化空间"
- "基于现有代码结构给默往设计新功能"
- "默往的某个入口或深链会影响哪些模块"
- "给默往做一个增长想法，但要结合现有产品结构"

## Guardrails

- Do not invent module names, route names, or entry links not present in the docs.
- Do not recommend solutions that ignore the current modular architecture.
- If evidence is missing from the RAG docs, say what is unknown.
