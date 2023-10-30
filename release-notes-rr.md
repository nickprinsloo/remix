## Minor Changes

### Fetcher Persistence/Cleanup Changes

Per this [RFC](https://github.com/remix-run/remix/discussions/7698), we've introduced a new `future.v7_fetcherPersist` flag that allows you to opt-into the new fetcher persistence/cleanup behavior. Instead of being immediately cleaned up on unmount, fetchers will persist until they return to an `idle` state. ([#10962](https://github.com/remix-run/react-router/pull/10962))

- This is sort of a long-standing bug fix as the `useFetchers()` API was always supposed to only reflect **in-flight** fetcher information for pending/optimistic UI -- it was not intended to reflect fetcher data or hang onto fetchers after they returned to an `idle` state
- With `v7_fetcherPersist`, the `router` only knows about in-flight fetchers - they do not exist in `state.fetchers` until a `fetch()` call is made, and they are removed as soon as it returns to `idle` (and the data is handed off to the React layer)
- Keep an eye out for the following specific behavioral changes when opting into this flag and check your app for compatibility:
  - Fetchers that complete _while still mounted_ will no longer appear in `useFetchers()` after completion. They served no purpose in there since you can access the data via `useFetcher().data`).
  - Fetchers that previously unmounted _while in-flight_ will not be immediately aborted and will instead be cleaned up once they return to an `idle` state. They will remain exposed via `useFetchers` while in-flight so you can still access pending/optimistic data after unmount.

### New Fetcher APIs

Per the same [RFC](https://github.com/remix-run/remix/discussions/7698) as above, we've introduced some new APIs that give you more granular control over your fetcher behaviors. ([#10960](https://github.com/remix-run/react-router/pull/10960))

- You may now specify your own fetcher identifier via `useFetcher({ key: string })`, which allows you to access the same fetcher instance from different components in your application without prop-drilling
- Fetcher keys are now exposed on the fetchers returned from `useFetchers` so that they can be looked up by `key`
- Add `navigate`/`fetcherKey` params/props to `useSumbit`/`Form` to support kicking off a fetcher submission under the hood with an optionally user-specified `key`
  - Invoking a fetcher in this way is ephemeral and stateless
  - If you need to access the state of one of these fetchers, you will need to leverage `useFetchers()` or `useFetcher({ key })` to look it up elsewhere

### Other Minor Changes

- Add support for optional path segments in `matchPath` ([#10768](https://github.com/remix-run/react-router/pull/10768))

## Patch Changes

- Fix the `future` prop on `BrowserRouter`, `HashRouter` and `MemoryRouter` so that it accepts a `Partial<FutureConfig>` instead of requiring all flags to be included ([#10962](https://github.com/remix-run/react-router/pull/10962))
- Fix `router.getFetcher`/`router.deleteFetcher` type definitions which incorrectly specified `key` as an optional parameter ([#10960](https://github.com/remix-run/react-router/pull/10960))

**Full Changelog**: [`6.17.0...6.18.0`](https://github.com/remix-run/react-router/compare/react-router@6.17.0...react-router@6.18.0)
