# CloudscribeNG

<img width="256" height="256" alt="image" src="/images/CloudscribeNG.png" style="display:inline-block; margin-right:10px;" />
<img width="256" height="256" alt="image" src="/images/CloudscribeNG_1.png" style="display:inline-block; margin-right:10px;" />
<img width="256" height="256" alt="image" src="/images/CloudscribeNG_2.png" style="display:inline-block;" />

**CloudscribeNG** is a community-driven continuation of the original **cloudscribe** open-source project — a mature, modular ASP.NET Core library ecosystem covering logging, database abstraction, content management, web components, and more.

## Why CloudscribeNG Exists

**cloudscribe** represented years of careful, community-driven development across dozens of repositories. In June 2026, the original project's current maintainers announced a move to closed-source licensing for future releases and indicated that they would move the repositories from public to private. That decision would have left the last open-source release frozen in time, with no path for the community to keep fixing bugs, adding features, or tracking the .NET platform forward, and access to the original open-source code.

Rather than let that body of work stagnate, **CloudscribeNG was created as a fork of the final open-source release of cloudscribe** — preserving all of its existing functionality while establishing a clear, independent path for continued open-source development. The name is deliberately a "strong, future-focused successor": it keeps the **cloudscribe** lineage recognizable, while clearly signalling that this is a new generation, not just a maintenance branch of the original.

## A Clean Break: Full Rename Across the Estate

To make CloudscribeNG safe to adopt alongside (or instead of) the original packages, **every repository, project, file, and namespace across the entire estate has been renamed** from `cloudscribe.*` to `CloudscribeNG.*`.

This was a deliberate, estate-wide decision, not just cosmetic branding:

- **No NuGet collisions.** The original `cloudscribe.*` packages still exist and are still installed in countless projects. Publishing packages under the same package IDs and namespaces would risk version conflicts, ambiguous upgrades, and broken builds for anyone consuming both.
- **No namespace collisions.** Renaming `namespace cloudscribe.*` to `namespace CloudscribeNG.*` (and updating every `using` statement, project reference, and solution file to match) means CloudscribeNG can be referenced side-by-side with legacy cloudscribe packages without type or assembly clashes.
- **Consistency across the board.** Folder names, `.csproj` files, `.sln` references, workspace configuration, and even code attribution strings were all updated together, so the estate is internally consistent rather than a patchwork of renamed and un-renamed pieces.

This migration was performed using a shared set of automation scripts (see the [BaseDev](https://github.com/CloudscribeNG/BaseDev) repository) that clone, rename, rewrite, build, and commit changes across every repository in the organization in a single coordinated pass.

## A Migration Path Back: `CloudscribeNG.Compatibility`

Renaming every namespace is the right long-term move, but we know it creates short-term friction for existing projects built against `cloudscribe.*`. To ease that transition, we will be building a **`CloudscribeNG.Compatibility`** layer that provides seamless namespace aliasing, so existing code written against the original cloudscribe namespaces can consume CloudscribeNG packages with minimal (ideally zero) source changes. The goal is to let teams upgrade to an actively maintained, open-source successor without a disruptive rewrite.

## Modernizing the Codebase

Beyond the rename, CloudscribeNG is actively working through years of accumulated cruft and outdated dependencies:

- **Pruning legacy code.** Dead code paths, obsolete workarounds, and unused abstractions that built up over cloudscribe's long history are being identified and removed, so the codebase stays maintainable going forward.
- **Replacing the MySQL provider.** `Pomelo.EntityFrameworkCore.MySql` — which had become stuck supporting only .NET 9 — has been replaced across the estate with `Microting.EntityFrameworkCore.MySql`, unblocking MySQL support on current and future .NET versions.
- **Rethinking package version pinning.** We're moving away from tightly pinned dependency versions and toward pulling in the most recent compatible packages on every build. This keeps the estate current with upstream security fixes and framework improvements instead of quietly drifting out of date.

## Building the Estate as a Whole

Because CloudscribeNG is split across many repositories that depend on one another, moving to "always pull the latest needed packages" only works if the *whole estate* is built and validated together. A shared package bumped in isolation in one repo can easily trigger downgrade or version-conflict errors in a downstream repo that hasn't been updated yet.

That's why our tooling ([BaseDev](https://github.com/CloudscribeNG/BaseDev)) builds every repository in dependency order against a shared local NuGet feed. This surfaces version conflicts immediately, in one run, rather than as a surprise weeks later in someone else's repo — making it safe to roll package version bumps forward across the entire organization with confidence.

## Roadmap

All of the CloudscribeNG repositories are private at this time, while we work through the rename and modernization process. Once that is complete, we will be opening up the repositories to public access and publishing packages to NuGet.org.

## Get Involved

CloudscribeNG is a community effort, and contributions are welcome — whether that's code, bug reports, documentation, or just spreading the word. Once we go public, check the individual repositories for contribution guidelines, and see [BaseDev](https://github.com/CloudscribeNG/BaseDev) for the tooling used to manage the estate as a whole.
