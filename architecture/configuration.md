# Spike Config

Most of the inner workings of `spike` will never be open to configuration, we
allow the users a limited interface for changing some of the default behavior of
`spike` for a smoother experience.

This configuration will be handled by a configuration object, which is exported
from a `spike.config.js` file in the project root.

## Scope

The configuration file will allow you to change the following options:

- Enabling typescript support. Optional, default to `false`. Should be under an `dangerous` or `experimental` key (synonyms).
- Disabling minification on `build`. Optional, default to `true`.
- Disabling source maps on `build`. Optional, default to `false`, set to false if minification is `false`.
- Fetch function for providing data to reshape templates. Optional, default to
    `null`
- Specifying vendor dependencies to be converted to "web_modules". An object of key value pairs, as `{ [key: string]: string }` representing the dependency name/id and the path to bundle.

Configuration will not allow you to:

- Change the source directory. You either follow `spike`'s conventions in the root directory, or `src` directory.
- Change the output directory.

### Example
```js
//spike.config.js

export default {
  minify: false,
  sourceMaps: true,
  experimental: {
    typescript: true,
  },
  data: myFetchFunction(), // user defined fetch logic
}
```

### Implementation

The config should be a standalone package that is used by the CLI, that takes in a valid configuration object, and applies validation to it.

The config should handle:

- The config should resolve the spike root (based on the presence of a spike.config, otherwise defaulting to the current working directory).
- The source directory logic (check for entries in `src`, otherwise spike root).
- The config should handle default values, and toggle logic (e.g, like sourcemaps being set to `false` if minification is set to `false`).
- The config should handle resolving the data given to it (either via an async function, function, or object)
- The config should handle resolving all of the possible routes for a dynamic route, by executing the dynamic route function logic or by validating the array passed to it.
- The config should handle resolving project dependencies to be converted to web modules, as an object of key value pairs (key/name and path to bundle):
  - If package.json is present, and `process.env.NODE_ENV !== "production"`, then use `devDependencies` and `dependencies` and `peerDepencencies`
  - If package.json is present, and `process.env.NODE_ENV !== "production"`, then use `dependencies` and `peerDepenedencies`
  - If a `dependencies` key is present in the spike config, combine it with the above

## Value

Spike will be opinionated and not require any configuration at all for most use
cases. The configuration object will only provide a limited interface for users
that want more fine grained control over how `spike` will behave in certain aspects.

## Risks & Rabbit Holes

- We must stick to our guns about keeping the options that can be configured
    limited to avoid configuration fatigue that plagues other projects.

## Appetite

1 person, 1 week.
