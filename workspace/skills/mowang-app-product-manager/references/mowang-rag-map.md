# Mowang RAG Map

Use this file to route product questions to the right RAG documents under `mowangrag/`.

## Read First

- `mowangrag/README.md`
  Use for overall product summary, ingestion order, and document map.

## Product Domains

- `mowangrag/01-project-overview.md`
  Use for module inventory, flavor differences, main tabs, and external entry points.

- `mowangrag/02-startup-runtime.md`
  Use for startup chain, privacy gate, splash logic, lifecycle dispatch, and main runtime responsibilities.

- `mowangrag/03-login-user-settings.md`
  Use for login methods, registration completion, user center, settings, security, and screen password.

- `mowangrag/04-im-social.md`
  Use for IM, secret chat, contacts, groups, group-send assistant, sharing backflow, circle of friends, and explore.

- `mowangrag/05-wallet-album-rtc.md`
  Use for wallet, payment, VIP, pretty number, Mowang coin, encrypted album, and RTC.

- `mowangrag/06-data-network-infra.md`
  Use for HTTP, socket, local storage, push, third-party SDKs, ads, and screen protection.

- `mowangrag/07-route-entry-inventory.md`
  Use for route inventory, provider routes, deep links, system share entry, and access rules.

## Task Routing

- Feature strategy for the four main tabs:
  Read `01-project-overview.md` and the matching domain file.

- Login to home conversion or onboarding:
  Read `02-startup-runtime.md` and `03-login-user-settings.md`.

- Messaging, private chat, contacts, group, sharing:
  Read `04-im-social.md`.

- Wallet growth, monetization, membership, asset features:
  Read `05-wallet-album-rtc.md`.

- Technical feasibility or product constraints:
  Read `06-data-network-infra.md`.

- Route cleanup, deep links, entry analysis, modular ownership:
  Read `07-route-entry-inventory.md`.

## Answer Pattern

When answering a Mowang-specific question:

1. State current product reality from the docs.
2. List affected modules, tabs, routes, or systems.
3. Propose the change.
4. Explain risks, dependencies, and a validation plan.
