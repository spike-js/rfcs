# Isomorphism RFC

## Table of Contents

1. Overview
1. Scope
1. Value
1. Risks & Rabbit Holes
1. Appetite

## Overview

While SSGs provide a lot of value, we are quickly learning that pre-rendering has a ceiling. Thus, in order to make spike as anti fragile as possible, we should ensure it supports all rendering strategies in a very simple and basic way. 

`spike` should provide a conventions based approach to:

- pre-rendering
- server-side rendering 
- client-side rendering

And allow this to be configurable on a per-route basis. 

## Scope

Spike does not need fancy rendering. 

It needs a very straight forward approach that the user can use to scale in the way the best makes sense to them. 

It needs to be a series of compositional abstractions that you can peel away to get full control and not require third party libraries or tooling. 

It needs to support pre-rendering, server-side rendering, and client-side rendering and be configurable on a per route basis. 

It needs to be simple, without magic. The more magic we introduce, the more fragile we make this feature. 

### Pre-rendering

Pre-rendering is the easiest part. At build time, we create the spike graph from the entry points. 

Inside this graph we store the raw content of each node. From this we can:

- convert the raw content of each HTML file to a reshape render function, and;
- use that render function to inject the site data at build time and get out static HTML with all of the dependencies resolved and linked
- the compiler can then write out the results as static HTML files and other assets in the output directory 

```
src/index.html => dist/index.html
public/favicon.ico => dist/favicon.ico
```

### Server-side rendering

Server-side rendering becomes the next easiest thing. 

- we can output the render functions as NodeJS compatible functions, and;
- also output a serverless ready function to call this logic for a given route
- this logic can be mapped to a redirect rule on a CDN to render pages on request, and enable incremental SSG like vercel by kicking off a new build 

```
src/index.html => dist/.spike/routes/index.js
               => dist/.spike/router.js
```

This enables users to:

- Server-side render pages where pre-rendering is too slow, without changing their code.
- Server-side render pages with personalization, w/o adding complicated client-side logic (such as having a "Good [morning|afternoon|evening], [full_name]" in their hero.

### Client side rendering

Client-side rendering is not a big lift after this, either.

- we can take the render function generated on the page, and;
- the data that was used by the page, and;
- write out a JS bundle and JSON file for the data
- this enables two simple workflows for doing client-side rendering of your spike pages, in the event you have data that changes often:
  - A data-driven approach
  - A template driven approach
  
 ```
src/index.html => dist/.spike/routes/index.js
               => dist/.spike/routes/index.mjs
```

### Data-driven Approach

The data driven approach will involve embedding the data for the page, and the render function, into the page via an IIFE (immediately invoked function expression).

This will:

- Fetch the build-time data for the page, and then embed it into a reserved `SPIKE` global variable.
- Create a JS proxy that watches for changes on the `SPIKE` global variable, and on change;
- Re-run the render function with the update `SPIKE` global data

This will allow users to easily do client-side rendering of their templates by writing simple logic that updates the contents of the `SPIKE` variable, while changing _nothing_ about the code of their site!

This enables users to:

- Encode in the logic for updating the page based on user interactions _without_ duplicating template code. They write their reshape templates to handle all cases, and then update the data at runtime to reflect.
- Handle different states for a page, such as light-mode and dark-mode, while keeping your markup concerns _in your HTML_, your styling concerns _in your CSS_, and your business logic in JS.

### Template-driven Approach

The template driven approach can be used _in conjunction_ with the data-driven approach. The template-driven approach allows the user to use the render function as they see fit to re-render the page with new data.

This more advanced workflow is useful for:

- Creating protected routes (having a loading state, and rendering the real page layout only when valid data is provided)
- Access other page layouts in the frontend, to create better perceived performance (such as fetching the render function and data for another page, and rendering it in-place instead of doing a real page refresh)
- A lot of other cool stuff JS SSGs do, without including the framework bloat

## Value

This project poses to be the best way to build a sustainable, low-maintenace website.

If Spike is to be future proof, it must take an opinionated approach, but it must not _ignore reality_. The reality is, pre-rendering is not a silver bullet. Even for small sites, there are scenarios where pre-rendering introduces a lot more complexity than it removes, and simply allowing a different rendering strategy is much more effective than hyper-optimizing for pre-rendering.

I believe that enabling the 3 most common rendering strategies will make Spike much more antifragile, and allow people to continue using it without ejecting for a long, long time.

## Risks & Rabbit Holes

- Browser support, the non-data-driven approach may only work in modern browsers that support ES Modules
- Content -- the serverless approach will need content written for every major CDN, because the user will be responsible for implementing the rewrite/redirect rule logic.
- Doing this too early; we need a working MVP of pre-rendering before we go any further.

## Appetite

`~20 hours`
