# Ejection RFC

## Value

Spike allows users to eject from the "promised land" to an unlocked project setup that we offer _zero_ support for. This is how users can customize w/o asking us to make breaking changes.

## Scope

In order to eject spike we have to do... a lot. :sweat:

Spike is built in a series of parts:

- The parser (which reads in the filesystem, and then passes it to the compiller)
- The compiler (which takes a series of in-memory files, and applies transformations to them, and pushes data through templates)
- The config (which configures the above)
- The CLI (which provides the interface to spike)

### Step 1: eject the parser and compiler

Doing this the same way CRA does would be silly -- we'd be duplicating logic. I propose instead, we wrap up the spike parser and compiler in a set of rollup plugins, which we make 100% clear we only fix bugs with, and will not take feature requests.

This would allow you to add the plugin to a rollup scripts and run the primary spike build processes:
- `rollup-plugin-filesystem-graph` => filesystem compiler, which adds "virtual files" to the rollup build
- `rollup-plugin-reshape` => wrap reshape into rollup plugin w/ simple configuration
- `rollup-plugin-postcss` with `rollup-plugin-postcss-preset-env` => just do it
- `rollup-plugin-esbuild` => just do it

## Step 2: eject the config

Just pull it into the rollup config and use it in the rollup plugins

## Step 3: the cli

Replace CLI commands with NPM scripts in a `package.json`. Obviously generate a `package.json` if it doesn't exist.

## Risks & Rabbit Holes

- Spending more time on this that actual Spike functionality
- This not matching pre-ejected functionality
  - We need to run an example site that gets ejected and then run our tests against it

## Appetite

2 people, 1-2 weeks
