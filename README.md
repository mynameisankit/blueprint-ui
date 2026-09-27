# Blueprint UI

<p align="center">
  <img src="./assets/blueprint-ui-logo.svg" width="136" alt="Blueprint UI logo" />
</p>

<p align="center">
  <strong>Give general-purpose LLMs the judgment to build with your private design system.</strong>
</p>

<p align="center">
  A headless Model Context Protocol (MCP) server for design-system discovery, guided composition, and UI-tree validation.
</p>

---

## The idea

Blueprint UI is not a React component library and it does not train a model. It is the reasoning layer between an external LLM (such as GPT or Claude) and a private component system.

It gives an LLM structured access to component knowledge, constraints, patterns, and validation—so the model can act less like it is guessing at UI and more like an engineer who knows the system.

```text
Natural-language UI request
          │
          ▼
     External LLM
          │  MCP
          ▼
     Blueprint UI
          │
          ├── Component registry & metadata
          ├── Usage and composition rules
          ├── UI patterns & design tokens
          └── UI-tree validation
          │
          ▼
  Structured UI DSL / tree
          │
          ▼
   React renderer (later)
```

## Initial milestone

The first release is completely headless. Its job is to make a valid structured UI tree possible, inspectable, and repairable—not to render pixels.

An external LLM should be able to:

1. Discover available components.
2. Search components from a user’s intent.
3. Learn when a component is appropriate (and when it is not).
4. Retrieve established UI patterns.
5. Compose a tree from valid components only.
6. Submit the tree for validation.
7. Receive actionable errors and repair it.

## Proposed MCP surface

| Tool                | Purpose                                                                            |
| ------------------- | ---------------------------------------------------------------------------------- |
| `list_components`   | Browse the component inventory and capabilities.                                   |
| `search_components` | Find components from a natural-language intent.                                    |
| `get_component`     | Read props, slots, accessibility notes, and usage guidance.                        |
| `get_patterns`      | Retrieve higher-level compositions such as forms, settings pages, or empty states. |
| `get_tokens`        | Inspect approved design tokens.                                                    |
| `validate_ui_tree`  | Validate a proposed UI DSL tree and return precise repair guidance.                |

## Design principles

- **Constrain, don’t hallucinate.** The server exposes explicit schemas and rules instead of asking the model to infer a system from examples.
- **Teach judgment, not only APIs.** Metadata includes intended use, anti-patterns, and composition constraints.
- **Validation is a conversation loop.** Errors should name the failing path, explain the rule, and offer a useful next move.
- **Renderer-agnostic core.** A React renderer consumes the validated tree later; the knowledge layer remains independently useful.
- **Small, inspectable foundations.** TypeScript, Node.js, Zod, and the current official MCP TypeScript SDK.

## Example UI DSL

```ts
const settingsPanel = {
    type: "Page",
    props: { title: "Profile settings" },
    children: [
        {
            type: "Form",
            children: [
                {
                    type: "TextField",
                    props: { label: "Display name", name: "displayName" },
                },
                {
                    type: "Button",
                    props: { variant: "primary", type: "submit" },
                    children: ["Save changes"],
                },
            ],
        },
    ],
};
```

The validator can reject unknown components, invalid props, disallowed parent/child combinations, missing required fields, token violations, and pattern-specific rules before anything reaches a renderer.

## Status

Early exploration. The next step is to define the component registry and UI-tree schema, then verify the current official MCP TypeScript SDK before wiring tools.

## License

TBD
