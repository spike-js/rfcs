# Alpha RFC

## Table of Contents

1. Overview
   - Who is this for?
   - What values does this project follow?
   - What principles does this project follow?
1. Scope
1. Value
1. Risks & Rabbit Holes
1. Appetite

## Overview

`spike` is the codename for this project.

### Who is this for?

This project is intended to create a standards-based website builder for:

- Small personal websites (3-5 page templates)
  - Blogs
  - Portfolios
  - Developer blogs
  - Et cetera
- Landing pages
  - Marketing pages
  - Ad landing pages
  - Et cetera
- Small business websites
  - Basically replace or compliment facebook pages

### What values does this project follow?

The `spike` project and maintainers align with the following values:

- **Antifragility:** the `spike` project should not be fragile. It should live on, as the market changes, without requiring work from its users.
- **Simplicity**: the `spike` project does not do everything. It meets the needs of the cohorts it is built for, and nothing more. Every feature we choose to not add, or choose to remove, is a win for the project.
- **Education**: the `spike` project should be a tool for learning the web. It shouldn't introduce enterprise abstractions to solve complexity that the cohorts of this project doesn't have.

### What principles does this project follow?

The `spike` project and maintainers align with the following principles, which are in accordance with our values:

- **Closed to extension**: 
  - It should be closed to extension, to prevent dependency hell. 
  - Advanced features are solved out of `spike`, not inside it.
  - _This will ensure a project built with `spike` today, works next year._
- **No breaking changes**: any change made to the project will extend, and potentially soft-deprecate old functionality. But a site built on day one should always still build.
- **Evergreen development**: 
  - It should be built on web standards, so that the tooling never dies.
  - It should handle changing tides without breaking. `spike` relies on as few dependencies as possible, and the user never manages those dependencies.
  - It should be designed to allow changing dependencies, without the user noticing an API change. We deal with the pain of dependency management, not our users. 
- **Documentation over code contribution**:
  - Problems solved outside of `spike`'s wheelhouse with `spike` should not become code; they should become content.
- **Come as you are, and grow beyond**:
  - `spike` asks people to come as they are, and write simple web standard code to build a website.
  - When someone's ability grows beyond `spike`'s built-in capabilities, we offer an escape hatch, instead of leaving them hanging out to dry.
  - Spike should scale to _traffic_ not to team complexity. Next.js is for enterprise teams, Spike is for small websites that get millions of visitors.

## Scope

For the purposes of the alpha, `spike` is/will:

- Closed to modification and extension, but open to ejection or forking
- Built on modern web standards, no pre-compiling:
  - ES6 JS, polyfilled with `esbuild` according to a `browserslist` config in `package.json`
  - Modern CSS, polyfilled with `postcss` and [postcss-preset-env](https://preset-env.cssdb.org/)
  - Modern HTML, with templating powered under the hood by [reshape](https://reshape.js.org/) and a few built-in [reshape plugins](https://github.com/reshape/standard) for templating
  - expressions
  - data injection via a reserved constant
  - one-way includes w/ variables
- Supports all web technologies as entrypoints for compilation:
 - HTML, CSS or JS can be treated as an entrypoint 
 - HTML only is the default, but can be overridden as a CLI command/API command.
- No built-in data management
  - `spike` expects a JS function to return structured data. What you do after that, is up to you.
  - _Note:_ we need to do a _little_ legwork to make sure tracking dependencies of the data on templates is possible, to enable incremental building.
    - This may mean enforcing a "graph" style approach for the data structure, too.
- Dependency-less
  - All dependencies are bundled with the package
- Stand-alone
  - `spike` will be packaged as an executable with a slim node-runtime for node-less execution (e.g, with `vercel/pkg`)

For the purposes of the alpha, `spike` is not/will not:

- Support pretty URLs; you need to structure your source code for that (without ejecting)
- Allow reshape plugins (without ejecting)
- Allow esbuild loaders (without ejecting)
- Concatenate/bundle JS files; it just transpiles files to the same structure in the output folder (without ejecting)

How this could work:

First, we'll setup `spike` to create a tree graph of the project's files and their dependencies.

```
onInstall:
=> receive package.json dependencies
  => run through esbuild, making es6 compatible
  => output to `web_modules`

onBuild:
=> receive entryPoints
  => check mimetype
    => html
      => add as parentless node
    => js
      => add as parentless node
    => css
      => add as parentless node
  => iterate over all all received entrypoints
    => is HTML
      => parse with reshape, receive AST
        => find inline JS; add as internal dependency node
        => find linked JS; add as external dependency node
        => find inline CSS (style); add as internal dependency node
        => find linked CSS (stylesheet); add as external dependency node
      => iterate through all children, applying the logic below based on mime type
      => run reshape-standard on final HTML, output
    => is JS
      => run through ESBuild, telling it treating every imported JS/TS file as an output file
    => is CSS
      => run through PostCSS, telling it to treat every imported CSS file as an output file
```

The graph this process generates would have signatures something like:

```
export interface Graph {
  nodes: Node[]
}

export interface Node {
  type: "internal" | "external" // in the file, or imported
  path: FilePath | InternalPath // how do I find this node?
  mimeType: "string" // what type of transformation does this node need?
  children: Node[] // what dependencies does this node have
}

export type FilePath = string;

export type InternalPath = {
  start: {
    line: number
    character: number
  }
  end {
    line: number
    character: number
  }
}
```

## Value

This project poses to be the best way to build a sustainable, low-maintenace website.

Currently the market is saturated by complex build tooling that is full of dependencies. This is a challenge, because these dependencies make it hard to come back to a project a year later without being inundated with dependency mismatches, missing dependencies, and more. All of this prevents a site built today from building next year.

This means that if this project succeeds, you could build websites that will work until the end of time, provided the web doesn't stop supporting:

- ES6 JS
- HTML5
- CSS3

## Risks & Rabbit Holes

- Getting excited about features. We need to stick to our guns. What is in, is in. Everything else is out.
- The graph implementation
  - It's complicated, but necessary to maintain speed.
- Browser support -- we gotta get this right, and make it configurable per-project based on a `browserslist` definition to reduce the footprint of builds.

## Appetite

12 weeks of work, by two people, working `~6 hours` per week.
