# Domain-first source organization

Use this playbook when the structure of a codebase should reflect its problem
domains rather than only its technical file types.

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
