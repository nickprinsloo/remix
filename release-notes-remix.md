## Minor Changes

### Vite!

Remix 2.2.0 adds unstable Vite support for Node-based Remix apps! Check out the "Future > Vite" section of the Remix Docs for details. ([#7590](https://github.com/remix-run/remix/pull/7590))

- `remix build` 👉 `vite build && vite build --ssr`
- `remix dev` 👉 `vite dev`

Other runtimes (e.g. Deno, Cloudflare) and custom servers (e.g., Express) are not yet supported.

### Fetcher Persistence/Cleanup Changes

Per this [RFC](https://github.com/remix-run/remix/discussions/7698), we've introduced a new `future.v3_fetcherPersist` flag that allows you to opt-into the new fetcher persistence/cleanup behavior. Instead of being immediately cleaned up on unmount, fetchers will persist in until they return to an `idle` state. ([#10962](https://github.com/remix-run/react-router/pull/10962))

- This is sort of a long-standing bug fix as the `useFetchers()` API was always supposed to only reflect **in-flight** fetcher information for pending/optimistic UI -- it was not intended to reflect fetcher data or hang onto fetchers after they returned to an `idle` state
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

## Patch Changes

- `@remix-run/react`: Fix warning that could be inadvertently logged when using route files with no `default` export ([#7745](https://github.com/remix-run/remix/pull/7745))
- `create-remix`: Support local tarballs with a `.tgz` extension which allows direct support for [`pnpm pack` tarballs](https://pnpm.io/cli/pack) ([#7649](https://github.com/remix-run/remix/pull/7649))
- `create-remix`: Default the Remix app version to the version of `create-remix` being used ([#7670](https://github.com/remix-run/remix/pull/7670))
  - This most notably enables easier usage of tags, e.g. `npm create remix@nightly`
- `@remix-run/express`: Allow the Express adapter to work behind a proxy when using `app.enable('trust proxy')` ([#7323](https://github.com/remix-run/remix/pull/7323))
  - Previously, this used `req.get('host')` to construct the Remix `Request`, but that does not respect `X-Forwarded-Host`
  - This now uses `req.hostname` which will respect `X-Forwarded-Host`

## Changes by Package

- [`create-remix`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/create-remix/CHANGELOG.md#220)
- [`@remix-run/architect`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-architect/CHANGELOG.md#220)
- [`@remix-run/cloudflare`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-cloudflare/CHANGELOG.md#220)
- [`@remix-run/cloudflare-pages`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-cloudflare-pages/CHANGELOG.md#220)
- [`@remix-run/cloudflare-workers`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-cloudflare-workers/CHANGELOG.md#220)
- [`@remix-run/css-bundle`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-css-bundle/CHANGELOG.md#220)
- [`@remix-run/deno`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-deno/CHANGELOG.md#220)
- [`@remix-run/dev`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-dev/CHANGELOG.md#220)
- [`@remix-run/eslint-config`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-eslint-config/CHANGELOG.md#220)
- [`@remix-run/express`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-express/CHANGELOG.md#220)
- [`@remix-run/node`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-node/CHANGELOG.md#220)
- [`@remix-run/react`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-react/CHANGELOG.md#220)
- [`@remix-run/serve`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-serve/CHANGELOG.md#220)
- [`@remix-run/server-runtime`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-server-runtime/CHANGELOG.md#220)
- [`@remix-run/testing`](https://github.com/remix-run/remix/blob/remix%402.2.0/packages/remix-testing/CHANGELOG.md#220)

---

**Full Changelog**: [`2.1.0...2.2.0`](https://github.com/remix-run/remix/compare/remix@2.1.0...remix@2.2.0)
