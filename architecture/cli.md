# Spike CLI

The CLI for Spike is a simple CLI interface over the core NodeJS API.

## Scope

The CLI will need to facilitate the following verbs/commands:

- Creating a site (`init`|`initialize`)
- Pack a sites `web_modules` (`pack` )
- Building a site (`build`)
- Running the development server (`dev`|`development`)
- Ejecting (`eject`)

The CLI should also have arguments for:

- Entrypoints (positional argument, 0)
- Typescript support (`--ts`|`--typescript`) - CHRIS DEMANDS IT (it's a first-party feature of esbuild, no dependencies required)
- Log output (`--log-level`|`-l`, `ERROR`, `WARNING`, `INFO`, `DEBUG`)

## Value

The CLI provides a simple place to get started with Spike, and leans heavily into the standalone `spike.exe` goal.

It allows non-technical people to generate a static site with only basic knowledge of HTML5 and CSS3.

## Risks & Rabbit Holes

- The CLI doesn't need to be fancy to start; keep it simple stupid (KISS)
- Don't add features. Add the commands, ensure they have good help text, and call it a day.

## Appetite

1 person, 1 week.
