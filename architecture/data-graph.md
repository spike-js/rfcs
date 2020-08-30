# Spike Data Graph

This outlines one way of approach the data graph in Spike, using Reshape's AST and `code` node to do so.

## Scope

The data graph is a graph describing each piece of data given to the site, and its relationship to the templates of the site.

Javascript has a feature called `proxies`, which let you add behaviour to an object when it is accessed. Given that reshape compiles its templates to functions that access data to render html, we could theoretically mutate the site data with a proxy each time it's passed to a template, with the template's metadata, and use logic to track which files access what parts of the data structure.

This would allow to:

- Compute the data access of a given template
- Grab all of the data that was accessed for a given template
- Hash it into a much smaller string
- Store the keys of the data mapped to each template, and a hash of their values, in a filesystem cache
- On subsequent builds, read in that cache, and compare the hash of current values against the old hash using the stored mapping, to see if the data has changed.

## Value

Incremental rebuild means:

- faster build and deployment
- less resources used
- faster development

## Risks & Rabbit Holes

- Not being able to accurately compute the data used by template before it is used poses the risk of the computation being out of date.

## Appetite

1 person, 1-6 weeks.
