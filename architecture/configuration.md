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

## Value

Spike will be opinionated and not require any configuration at all for most use
cases. The configuration object will only provide a limited interface for users
that want more fine grained control over how `spike` will behave in certain aspects.

## Risks & Rabbit Holes

- We must stick to our guns about keeping the options that can be configured
    limited to avoid configuration fatigue that plagues other projects.

## Appetite

1 person, 1 week.
