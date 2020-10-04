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

### Server-side rendering

Server-side rendering becomes the next easiest thing. 

- we can output the render functions as NodeJS compatible functions, and;
- also output a serverless ready function to call this logic for a given route
- this logic can be mapped to a redirect rule on a CDN to render pages on request, and enable incremental SSG like vercel by kicking off a new build 



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
