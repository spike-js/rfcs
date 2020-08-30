# Spike Data Graph

This outlines one way of approach the data graph in Spike, using Reshape's AST and `code` node to do so.

## Scope

The data graph is a graph describing each piece of data given to the site, and its relationship to the templates of the site.

Reshape provides us a way of using a rudimentary form of reverse analysis. Given that the data being passed into the site must be a well-known, 
non-recursive data structure, it can be `JSON.stringified` and analyzed. 

Inversely, given that reshape can calculate an AST of the expressions and usage of code in its AST, we can analyze what what data is used by the reshape templates.

With these two pieces of information, we can:

- Calculate the data needed for a given source page, and what its output page(s) will be
- Hash the data needed for a given source page into a much smaller string, the output data, and the current calculated paths for it as well
- Write that to a filesystem cache, mapping it to its source page by filePath
- On subsequent builds:
  - re-calculate dynamic paths, and see if any are added or removed
  - for matching paths, compare the hash of the current data against the previous hash, to see if it has changed
- Only re-do compilation for the outputs that have changed, or are new, and restore only unchanged outputs from cache

This means very effective incremental building may be possible, allowing full incremental rebuilds for prod.

## Value

Incremental rebuild means:

- faster build and deployment
- less resources used
- faster development

## Risks & Rabbit Holes

- We don't know what we don't know. We may learn this is not as useful as expected, or not possible without some non-ideal compromise.
- This could take a while; it shouldn't be prioritized over other features that will allow us to launch spike 2 sooner.

## Appetite

1 person, 1-6 weeks.
