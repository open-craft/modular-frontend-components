# Modular frontends

## Background

A couple of issues have been identified with the current MFE architecure that
we hope to resolve with this proposal. Those issues are:

- Duplicated javascript and styling code across multiple MFEs that needs to be
  loaded multiple times.
- Large bundle size for each MFE that includes the entire codebase instead of
  the smaller subset of features needed.
- Use of different libraries, versions of libraries etc across MFEs further
  increasing overall bundle size.
- An increasing number of different MFEs to keep track of, build and update.
- Poor customisability due to the lack of plugins, or other hooks into MFEs
- No seamless routing between MFEs.

## Proposal

Our proposal boils down to the following:

- Creating of an LMS mono-repo that pulls in MFEs related to the LMS into a
  single repo, potentially starting as submodules and eventully being hosted in
  the main repo.
- Use of webpack module federation to provide both a mechanism to support loose
  dependencies between MFEs, and for plugins
- Updating frontend-platform to support a single shared global state for auth
  etc, and an mfe-level state.
- Update to react-route v6 or alternative that supports relative routes, so that
  the parent MFE can provide a base route, and the MFE can handle routing inside
  that.
- Use of npm workspaces to have each MFE continue to be a separate package but
  dependencies shared across all MFEs can be added to the parent package and
  updated globally there.
- All MFEs canbe built in a single package, but can deployed separately.
- We can use something turbo to do cached builds on the CI so that only changed
  components  are built.
- The MFE config API can provide information about each MFE as a plugin, and
  this can be done automatically by the LMS based on URLs entered there, but
  can still be overridden on a per site level.
- Alternatively, standard MFEs can be linked together through direct import via
  module federation.
- Over time more smaller components / pages can be moved into thier own export
  scope within each MFE.

# Why module federation?

- It requires comparitively fewer changes to the current system.
- It supports SSR
- It allows direct communication between components through passing props,
  functions etc.
- MFEs can be further split down at multiple levels. For instance the learning
  MFE can embed a discussion thread without loading any other components.


# Why a share npm workspace/ monorepo

- For all apps to use the same dependency version.
  If any dependency changes, the tests for all apps will need to pass anyway.
  The preson perfoming the dependency upgrade is best suited to upgrade it for
  all MFEs due to having the most context. It'll need to be upgraded later
  anwyay.
- Shared dependency version further increase code shared, and overall bundle
  size.
- Shared theme code allowing a single build of the theme to be imported across
  all MFEs.
_ You can still go into each individual MFE/component and spin it up
  individually, or go to the root and start them all.
