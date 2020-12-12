# Spike Components

## Table of Contents

1. Overview
1. Scope
1. Value
1. Risks & Rabbit Holes
1. Appetite

## Overview

Spike components are a lightweight wrapper around Custom Elements to:

- Allow using reshape as a "render" function
- Allow using CSS parsed w/ PostCSS in the component
- Add implicit "DI" in spike templates -- if you have a component named `<cool-component>` and `<cool-component>` is present in an HTML file, then that component JS is added and executed on that page
This will allow spike sites to be built through composition, adhering to principles that programmers and designers working in the frontend have embraced in recent years.

E.g., [Component-based Design](https://opensenselabs.com/blog/articles/uxprinciples-cbs), [Atomic Design](https://bradfrost.com/blog/post/atomic-web-design/)

## Scope


Spike will support a "components" folder, similar to the "pages" folder, that allows you to:

- Place a JS file whose default export is a Custom Element wrapped with a `Define` method (an abstraction on CustomElementRegistry.define)
- Optionally place a matching CSS file that will be added to the Custom Element as a JS stylesheet
- Optionally place a matching HTML file that will be used as the "render" template for the component, with access to a component instances "attributes" or "data"

## Value

This allows compositional design in a very natural way (versus using reshape layouts, etc). Pages will also act as components.

This will:

- Allow atomic design and composition, like React, but without the cruft.
- Enable workflows where a single list of spike components can be used across multiple spike sites
  - This will also support _any_ custom element => just export it wrapped with the `Define` method from spike.

## Risks & Rabbit Holes

- KISS - we're not building a SPA, we're just embracing component based design to make sites easier to build, maintain, and reason about
- Isomorphism -- we need a way for spike components to run in server-process for pre-rendering.
  
## Appetite

6-12 hours.
