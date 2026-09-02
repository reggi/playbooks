# Domain-first source organization

Use this playbook when the structure of a codebase should reflect its problem
domains rather than only its technical file types.

This follows Domain-Driven Design (DDD), a software development approach that
focuses on aligning code with the real-world business environment and its rules.
The domains here are bespoke to each application: they are not the generic
folders software usually ships with, but names that only appear once you define
what a particular application is about.

## Timing

- Do not force a domain structure before meaningful patterns exist.
- Early in a project, allow the code to reveal its concepts, responsibilities,
  and recurring groups.
- Once identifiable domains emerge, name them and reorganize the code around
  them.
- Treat the domain structure as something that can become clearer over time.
  Update this document and earlier organizational decisions when the model
  improves.

## Structure

- Prefer a hierarchical filesystem organized by domain.
- Use directory nesting to show hierarchy, ownership, and relationships between
  concepts.
- Keep a feature's footprint colocated so its files can be found, changed, or
  removed together.
- Keep code that belongs to the same concept together, even when it contains
  different technical file types such as components, controllers, models,
  scripts, or HTML.
- Use technical groupings inside a domain only when they improve clarity.
- Avoid a top-level structure organized only by file type when it separates
  closely related behavior.
- Node.js does not impose one fixed source layout. Deliberately devise a
  structure based on the project's domains instead of accepting an arbitrary
  generic folder structure.

For example, prefer:

```text
cat/
  say.js
  look.js
dog/
  say.js
  look.js
```

over:

```text
say/
  cat.js
  dog.js
look/
  cat.js
  dog.js
```

when `cat` and `dog` are the meaningful domains. Adding or changing the `cat`
feature should primarily affect files within `cat/`, keeping its footprint
visible and contained.

Commands and subcommands should also use the filesystem to express their
relationship:

```text
commands/
  user/
    create.js
    delete.js
  project/
    create.js
    deploy/
      start.js
      status.js
```

## Public mappings

When a source concept is exposed through another interface, the public-facing
name should match the source code's domain, hierarchy, plurality, and
vocabulary. Do not paraphrase, shorten, rename, or collapse source concepts
during the mapping.

This applies to command names, routes, package exports, API paths, event names,
configuration keys, generated artifacts, and any other mapping from source code
to a public-facing name.

For example, this command mapping is bad:

```json
{
  "railway-docker-image-updates": "dist/railway/docker-images/check-updates.js",
  "repository-check": "dist/repository/check.js",
  "repository-fix": "dist/repository/fix.js"
}
```

`railway-docker-image-updates` changes `docker-images` to `docker-image` and
collapses `check-updates` to `updates`. The public name no longer describes the
source code directly. Prefer the direct mapping:

```json
{
  "railway-docker-images-check-updates": "dist/railway/docker-images/check-updates.js",
  "repository-check": "dist/repository/check.js",
  "repository-fix": "dist/repository/fix.js"
}
```

File-based routing demonstrates the same principle. Astro routes such as:

```text
pages/
  [param].js
  love/
    [name].js
```

make URL hierarchy and dynamic parameters understandable from the directory
tree. Domain-first source organization should provide the same kind of visible
map for the rest of the system.

## Domain contracts

- Files serving the same role across domains should have a consistent shape.
- Similar files should expose the same required exports, classes, functions,
  variables, or entry points where practical.
- Be consistent about named exports, default exports, and public types.
- In TypeScript, place a shared `domain.ts` above related domain folders. It
  should define the interfaces that equivalent files in each domain must
  satisfy.
- Use language-appropriate contracts for Bash, JavaScript, TypeScript, React,
  and other environments. The principle matters more than a specific syntax.

Example:

```text
domain.ts
cat/
  say.ts
  look.ts
dog/
  say.ts
  look.ts
```

Here, `domain.ts` defines the shared `Say` and `Look` interfaces. Both
`cat/say.ts` and `dog/say.ts` follow `Say`, while both `look.ts` files follow
`Look`.

## Worked example: a bespoke domain

Imagine an application that renders a 3D UI of the heart and the complications
that affect how it pumps. `heart-complications` is not a folder software
typically has. It only appears because this application is defined around that
subject, and once it is, a logical hierarchy of the domain emerges.

The interface lists each complication: `Arrhythmia`, `Aneurysm`, and `Stroke`.
Each is presented the same way, so each maps to a file under the same domain,
and each satisfies the same domain interface:

```text
domain.ts
heart-complications/
  arrhythmia.ts
  aneurysm.ts
  stroke.ts
```

Here `domain.ts` defines the `HeartComplication` interface. `arrhythmia.ts`,
`aneurysm.ts`, and `stroke.ts` each satisfy it, so every complication is uniform
in shape. What the UI, CLI, image, or other interface presents — and sometimes
what it deliberately does not present — should match the source code directly:
the same names, the same hierarchy, the same plurality, and the same vocabulary
described in Public mappings.

Later, when `brain-complications` is added, the same modelling repeats:

```text
domain.ts
heart-complications/
  arrhythmia.ts
  aneurysm.ts
  stroke.ts
brain-complications/
  aneurysm.ts
  stroke.ts
```

Once `heart-complications` is well-defined and `brain-complications` follows the
same pattern, a common shape becomes visible. A refactor can then introduce a
shared `BodilyComplication` interface that both `HeartComplication` and
`BrainComplication` extend, promoting the contract into `domain.ts` without
changing what each interface presents. Do not force this shared interface early;
let it emerge once more than one domain proves the pattern.

## Agent guidance

When given this document, an agent should:

1. Inspect the existing code before proposing domains.
2. Identify recurring concepts, responsibilities, vocabulary, and file shapes.
3. Distinguish real domains from incidental directories or temporary patterns.
4. Describe the inferred domain model before performing a large refactor.
5. Move related code together and make equivalent files structurally
   consistent.
6. Introduce explicit domain contracts where they clarify required behavior.
7. Preserve behavior, public APIs, and dependency direction during the
   refactor.
8. Update this document when the code reveals a clearer domain model.

The goal is for the filesystem itself to explain the system. A reader or agent
should be able to understand the major domains, their responsibilities, and
the expected shape of their implementations by examining the directory tree
and each domain's contract.
