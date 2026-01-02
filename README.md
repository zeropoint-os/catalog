# Zeropoint Catalog

This repository is the **official Zeropoint catalog**.

It defines a curated, reproducible set of **Zeropoint Modules (ZPMs)** and **Bundles** that can be installed on Zeropoint OS systems.

The catalog is intentionally simple, auditable, and conservative by design.

---

## What this catalog is

- A **git-backed index** of installable Zeropoint components
- A **trust boundary** between users and executable code
- A **reviewed source of truth** for what Zeropoint installs

The catalog does not host binaries, images, or artifacts.
It only points to source repositories at **exact, immutable commits**.

---

## What this catalog is not

- Not a registry
- Not a package manager
- Not a marketplace
- Not dynamic or auto-updating

Nothing in this catalog moves unless a human changes it.

---

## Why the catalog exists

Zeropoint runs real software on real machines.
That means reproducibility and trust matter more than convenience.

The catalog exists to ensure:

- Every install is deterministic
- Every change is reviewable
- Every rollback is possible
- No symbolic refs, branches, or floating versions are ever executed

This keeps Zeropoint boring in the ways that matter.

---

## Catalog structure

The catalog contains two top-level concepts:

- **Modules** — atomic, installable units
- **Bundles** — declarative compositions of modules

Both are expressed as YAML.

---

## Module format

A **module** defines how a single installable unit is fetched and installed.

Example:

```yaml
name: ollama
repo: https://github.com/zeropoint-os/ollama
commit: 4c432cf4ea65fb35cfe22833a0191872e5383c2a
type: terraform
description: Local LLM runtime
```

### Module fields

- `name`
  Stable identifier for the module.

- `repo`
  Git repository URL.

- `commit`
  **Required.** A full 40-character git commit SHA.
  Symbolic refs (branches, tags, HEAD) are not allowed.

- `type`
  Installation mechanism.
  Currently supported: `terraform`.

- `description`
  Human-readable summary.

### Guarantees

- The referenced code cannot change
- Installs are fully reproducible
- Catalog diffs precisely describe behavior changes

---

## Bundle format

A **bundle** defines how multiple modules are installed, linked, and exposed. It is a convenience that allows a user to compose the full functionality of an end-to-end workflow, while not having to re-invent all the pieces that compose it.

Example:

```yaml
name: local-ai-chat
modules:
  - ollama
  - openwebui

links:
  openwebui-ollama:
    - module: openwebui
      bind:
        ollama_endpoint: "${ollama.ollama_api_url}"

exposures:
  openwebui-http:
    module: openwebui
    protocol: http
    module_port: 8080
    description: OpenWebUI chat interface
```

### Bundle concepts

#### Modules
A list of module names to install.

#### Links
Named wiring definitions that bind outputs of one module to inputs of another.

Links inject concrete values at install time.
They do not describe network topology.

#### Exposures
Named, externally accessible services managed by Zeropoint’s proxy layer.

Exposures are addressable resources and have stable identities.

---

## Immutability and updates

The catalog does not auto-update.

To change behavior:
- a commit hash must be updated
- a PR must be reviewed
- a new catalog version must be published

This makes changes explicit and intentional.

---

## Trust model

Zeropoint assumes:

- Each PR to the catalog is reviewed by humans
- The agent enforces immutability
- The client does not execute logic

The catalog is the **default and recommended** path for introducing code into Zeropoint. Advanced users may install modules outside the catalog, but those installs are explicitly unmanaged and outside the catalog’s trust boundaries.

---

## Design principles

- **Explicit over clever**
- **Deterministic over convenient**
- **Declarative over imperative**
- **Local-first by default**

These constraints are intentional.

---

## Versioning

This catalog is versioned via git.
Zeropoint agents may pin or roll back catalog versions as needed.

---

## Summary

This catalog exists to make Zeropoint installs:

- predictable
- reviewable
- repeatable
- trustworthy

If a change cannot be expressed as a catalog diff, it does not belong here.
