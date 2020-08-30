# Spike Data Graph

This outlines one way of approaching the data graph in Spike, using Javasscript proxies to track the real time access to data. 

## Scope

The data graph is a graph describing each piece of data given to the site, and its relationship to the templates of the site.

Javascript has a feature called `proxies`, which let you add behaviour to an object when it is accessed. Given that reshape compiles its templates to functions that access data to render html, we could theoretically mutate the site data with a proxy each time it's passed to a template, with the template's metadata, and use logic to track which files access what parts of the data structure.

This would allow to:

- Compute the data access of a given template after a compilation
- Grab all of the data that was accessed for a given template
- Hash it into a much smaller string
- Store the record of the data mapped to each template, a hash of their values, and their output contents in a filesystem cache
- On subsequent builds, read in that cache, and:
  - re-calculate dynamics paths, and check which are new or deleted from the cache
  - for those that exist in the cache, compare the hash of current values against the old hash using the stored mapping, to see if the data has changed
- Only apply compilation for files that have changed or are new, and restore only files that are unchanged (not deleted) from the cache

## Value

Incremental rebuild means:

- faster build and deployment
- less resources used
- faster development

Proxies mean: 
- 100% accurate data graph, provided templates don't change.

## Risks & Rabbit Holes

- Not being able to accurately compute the data used by template before it is used poses the risk of the computation being out of date.

## Appetite

1 person, 1-6 weeks.
