# Spike Graph RFC

## Table of Contents

1. Overview
1. Scope
1. Value
1. Risks & Rabbit Holes
1. Appetite

## Overview

The `spike` graph is how we will enable future-proofing the parsing and compile strategy for Spike. It will allow us to create an accurate representation of the users _content_ and _templates_ instead of their representation on the filesystem, and allow us to convert the representation to formats better suited for a specific task.

## Value

To start, let me ask a question: what is a Spike project?

At the most basic level, it is:
- XML/HTML templates: which becomes HTML files, or other text-based formats using an XML templating syntax
- The data of the website
- CSS files
- JS files

But in reality, what the developer of the site cares about, and what we care about, is the _relationships_ between these items. E.g.:

- Which XML templates use what data?
- Which XML templates reference other XML templates?
- Which XML files reference CSS or other CSS files?
- Which CSS files reference other CSS files?
- Which XML files reference JS or other JS files?
- Which JS files reference other JS files?

With this information, we can do a _lot_ of fantastic optimizations for our users, like:

- Incremental building by computing the data provided in the last build to the current build, and only recompiling that which depends on new or changed data
  - This improves the speed of development, but also may allow us to do incremental production builds, too
- Broken link calculation by computing which HTML files are referenced in an XML/HTML template but don't exist in the final computed output
- Inline CSS/JS transpilation inside of HTML templates
- Have the confidence that multiple compilation events can be done in parallel by knowing they have no dependencies
- Do compilation of the graph in-memory instead of on the file system, allowing compilation to _fail_ much faster, creating shorter feedback loops for developers

## Scope

We'll need to refactor the parser logic to build a graph representation, instead of a flat graph, and then build in logic to convert that true graph representation back into the flat graph.

This graph implementation will look like:

```
type Graph = {
 nodes: GraphNode[]
 edges: Edge[]
 }

type GraphNode = {
  id: GraphNodeId;
  type: 'internal' | 'external';
  nodeLocation: string | InternalPath;
  mimeType: string;
  data?: unknown;
}

type GraphNodeId = string;

type Edge {
 from: GraphNodeId
 to: GraphNodeId
 edgeLocation: InternalPath
}

type InternalPath = {
  start: {
    line: number;
    col: number;
  }
  end: {
    line: number;
    col: number;
  }
};
```

This would look like this, for example:

```
{
  nodes: [
    { 
      id: "test.html", 
      type: "external", 
      nodeLocation: "test.html", 
      mimeType: "html",
      data: {} // this would be the reshape AST
    },
    {
      id: "test.css",
      type: "external",
      nodeLocation: "test.css",
      mimeType: "css",
      data: {} // this would be the postcss AST
    }
  ],
  edges: [
    { 
      from: "test.html", 
      to: "test.css", 
      edgeLocation: { 
        start: { line: 1, col: 0 },
        end: { line: 1, col: 13 }
      }
    }
  ]
}
```

## Risks & Rabbit Holes

- We need to benchmark how long it takes do computation now vs after, and see if we're getting true benefits:
  - Parse a flat graph currently
  - Parse the true graph
  - Parse the true graph into the flat graph

## Appetite

3-5 days of work, working `~6 hours a day`
