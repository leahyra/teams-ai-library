---
title: LLMs.txt
description: Links to LLM context files that provide coding assistants with documentation for the Teams SDK.
ms.topic: how-to
zone_pivot_groups: dev-lang
ms.date: 02/13/2026
---

# LLMs.txt

::: zone pivot="csharp"
A common practice to speed up development is using Coding Assistants. To better facilitate this usage, you can provide your coding assistant sufficient context about this SDK by linking your assistant to the SDK's llms.txt files for C#:
::: zone-end

::: zone pivot="python"
A common practice to speed up development is using Coding Assistants. To better facilitate this usage, you can provide your coding assistant sufficient context about this SDK by linking your assistant to the SDK's llms.txt files for Python:
::: zone-end

::: zone pivot="javascript"
A common practice to speed up development is using Coding Assistants. To better facilitate this usage, you can provide your coding assistant sufficient context about this SDK by linking your assistant to the SDK's llms.txt files for TypeScript:
::: zone-end


::: zone pivot="csharp"
**Small**: [llms_csharp.txt](https://microsoft.github.io/teams-sdk/llms_docs/llms_csharp.txt) - This file contains an index of the various pages in the C# documentation. The agent needs to selectively read the relevant pages to answer questions and help with development.

**Large**: [llms_csharp_full.txt](https://microsoft.github.io/teams-sdk/llms_docs/llms_csharp_full.txt) - This file contains the full content of the C# documentation, including all pages and code snippets. The agent can keep the entire documentation in memory to answer questions and help with development.
::: zone-end

::: zone pivot="python"
**Small**: [llms_python.txt](https://microsoft.github.io/teams-sdk/llms_docs/llms_python.txt) - This file contains an index of the various pages in the Python documentation. The agent needs to selectively read the relevant pages to answer questions and help with development.

**Large**: [llms_python_full.txt](https://microsoft.github.io/teams-sdk/llms_docs/llms_python_full.txt) - This file contains the full content of the Python documentation, including all pages and code snippets. The agent can keep the entire documentation in memory to answer questions and help with development.
::: zone-end

::: zone pivot="javascript"
**Small**: [llms_typescript.txt](https://microsoft.github.io/teams-sdk/llms_docs/llms_typescript.txt) - This file contains an index of the various pages in the TypeScript documentation. The agent needs to selectively read the relevant pages to answer questions and help with development.

**Large**: [llms_typescript_full.txt](https://microsoft.github.io/teams-sdk/llms_docs/llms_typescript_full.txt) - This file contains the full content of the TypeScript documentation, including all pages and code snippets. The agent can keep the entire documentation in memory to answer questions and help with development.
::: zone-end

