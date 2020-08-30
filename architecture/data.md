# Spike Data API

The Spike Data API allows people to inject any data into the site to be used on any page. 

Spike smartly learns where that data is used, to help do performance optimizations for the user, instead of them having to manage that themselves.

## Scope

Users will need a way to add data to their sites. Instead of shipping with abstractions for this in the box, we'll have the core of spike only receive data from the user's own code.

However, we have three key constraints I would like us to consider: 

1. Being able to calculate a graph of data used in html pages
  - Doing so will allow us to incrementally rebuild these pages, by computing a data hash against every template based on the used data, and then fetching that data prior to compiling and comparing that to the hashes.
2. Dynamic routing, allowing users to generate multiple pages based on an array of data passed in
3. Drafts. Making it easy to exclude data from being output on production builds, but leave them in development builds, to make using remote data easier.

This should guarantee we can skip an entire compiler run, or the parts that haven't changed, and provide dynamic SSG features through a very, very simple API.

### Implementation

I propose we add a simple key to `spike.config.js` that takes an object or function. This function returns all of the data for the site as an object:

```
/** 
  Give an object
**/
export const config = {
  data: {
    foo: "bar"
  }
}

/** 
  Or a function
**/
export const config = {
  data: async () => {
    return await fetch("https://example.com/example.json";
  }
}
```

Then, for dynamic routing, I propose we take a filename convention approach, similar to Next.js. A page is like a route, and a route can be:

- Static, mapping 1:1 with a single file in the build directory. `index.html => build/index.html`
- Dynamic, mapping 1:N to many files in the build directory. `[key].html => build/[key].html`
- Catch-all, mapping 1:N directories and files in the build directory. `{key}.html => build/[key[0]]/[key[1]].html`

A dynamic or catch all routes are configured via a function passed as a prop to the dyamic data function in spike's config, according to the name of the file. For example, for `[post].html` and `{doc}.html`:

```
export const config = {
  data: (spike) => {
    spike.dynamicRoute("post", function() {
      // return the valid format
    });
    
    spike.catchAllRoute("doc", function() {
      // return the valid format
    });
  },
}
```

The `dynamic` and `catchAllRoute` function expect an array to be returned in a specific format for each item:

```
type DynamicRoute {
  slug: string
  draft: boolean  
  [key: string]
}

type CatchAllRoute {
  slug: string[]
  draft: boolean
  [key: string]
}
```

For example:

```
export const config = {
  data: (spike) => {
    const allData = fetch("https://example.com/example.json");
    const now = new Date();
  
    spike.dynamicRoute("post", () => allData.posts.map(post => ({
      ...post,
      slug: post.href,
      draft: post.publish_date > Date.now()
    })));
    
    return allData;
  }
}
```

Finally, to start we will offer no data abstractions (like markdown parsing) until it is clear that it's worth baking in.

## Value

This very simple approach to data makes spike extremely approachable. Users need a very basic understanding of HTML, CSS, Javascript and the filesystem to use Spike, while getting all of the advantages of more complicated SSGs.

It also means that getting data into spike is as simple as following any guide for data fetching with Javascript for any tool.

Once you do that, you simply map arrays of data to generate pages dynamically, and use whatever data you need in your templates from the data you returned.

## Risks & Rabbit Holes

- Don't add features. Keep it dead simple.
- Incremental data may require a non-breaking, but completely new API, if we don't investigate it first. We need to test extracting data dependencies from resharp's API againt remote data sources.

## Appetite

1 person, 1 week.
