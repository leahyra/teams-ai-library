---
title: 🤖 AI
description: Overview of AI components in Teams SDK, including Prompts for orchestration and Models for LLM interfaces.
ms.topic: how-to
ms.date: '2026-02-24'
zone_pivot_groups: dev-lang
---

# 🤖 AI

The AI packages in this SDK are designed to make it easier to build applications with LLMs.
The :::zone pivot="typescript" inline :::`@microsoft/teams.ai` package:::zone-end:::zone pivot="csharp" inline :::`Microsoft.Teams.AI`:::zone-end:::zone pivot="python" inline :::`microsoft-teams-ai` package:::zone-end has two main components:

## 📦 Prompts

A `Prompt` is the component that orchestrates everything, it handles state management,
function definitions, and invokes the model/template when needed. This layer abstracts many of
the complexities of the Models to provide a common interface.

## 🧠 Models

A `Model` is the component that interfaces with the LLM, being given some `input` and returning the `output`.
This layer deals with any of the nuances of the particular Models being used.

It is in the model implementation that the individual LLM features (i.e. streaming/tools etc.)
are made compatible with the more general features of the :::zone pivot="typescript" inline :::`@microsoft/teams.ai` package:::zone-end:::zone pivot="csharp" inline :::`Microsoft.Teams.AI`:::zone-end:::zone pivot="python" inline :::`microsoft-teams-ai` package:::zone-end.

> [!NOTE]
> You are not restricted to use thezone pivot="typescript" inline :::`@microsoft/teams.ai` package:::zone-end:::zone pivot="csharp" inline :::`Microsoft.Teams.AI`:::zone-end:::zone pivot="python" inline :::`microsoft-teams-ai` package:::zone-end to build your Teams Agent applications. You can use models directly if you choose. These packages are there to simplify the interactions with the models and Teams.
:::
