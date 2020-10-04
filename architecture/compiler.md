# Compiler RFC

## Overview

The `spike` compiler is the package that compiles a `spike` graph to a deployable static web site/application.

## Scope

The spike compiler will take a `spike` graph and spike config as an input, and output a working static web site, or throw, notifying the user as to what went wrong, and directing them where to fix the problem.

The spike compiler will expect the following of the graph given as input:

- Each node has the raw format of the node under a specific key of the `data` attribute:
  - `postcss` for `postcss` AST
  - `reshape` for `reshape` AST
  - `html` for raw `html` string
  - `css` for raw `css` string
  - `js` for raw `js` string

The spike compiler will also expect the config to provide an object of project dependencies, or an empty object, which it will use to transpile web_modules.

### Web Modules Phase

The spike compiler will take a list of dependencies with a name and path, and compile their contents to ES module compatible JS in the `web_modules` folder. This folder will then be copied over to the output directory as static assets.

We will need to keep a JSON manifest of the version that is stored in `web_modules`, so we can skip the compilation step if the correct version is already present in version control, and allow this logic to be bypassed for debugging.

### Static Copy Phase

Before compiling, we will have a static copy phase which will:

- copy static assets in the `[src/]public` folder as-is to the output folder.
- copy the web_modules to the output folder.

### Compile phase 

The compiler will then traverse the graph's edges, calculating the _shortest_ path for computing the dependencies, going in both directions...

Essentially this means we'll compute the result of child dependencies that share the same _inputs_ in isolation, and then re-use that computation for other parents that use that child.

This means that all compilation will happen _only once_, and in the _shortest amount of cycles possible_ leading to the fastest possible build. This shortest path traversal also means that we can parallelize the build, by doing the compilation of children _first_, meaning no parent will be compiled until its children have already been compiled, guaranteeing we can compile all parents in parallel.

The results will be computed in memory, and then in no failures occur, will be written out to the output directory.

The compile phase _should warn_ if any output is mapped to the `public` or `web_modules` folder, and _be ignored entirely_.

## Value

The value of the compiler is a working static web site, built on native web tech, in the smallest size possible.

It will _hopefully_ also mean we're the fast JS SSG in the west.

## Risks & Rabbit Holes

- Paying attention for speed. Make it work, make it good, and _then_ make it fast.
- Paying attention to performance/site output size. Make it work, _then make it good_.
- Web Modules. We have no idea what kind of issues we'll face trying to convert node_modules to production ready web modules.

## Appetite

`30 hours`
