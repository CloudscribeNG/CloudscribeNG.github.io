# CloudscribeNG

<img width="256" height="256" alt="image" src="https://github.com/user-attachments/assets/bf5233ee-f2f4-4d1d-aba3-5507faad5003" />
<img width="256" height="256" alt="image" src="https://github.com/user-attachments/assets/10bb5632-c26a-4717-bd5a-cb384c960480" />
<img width="256" height="256" alt="image" src="https://github.com/user-attachments/assets/aa95d3a7-48da-485f-8a3e-bb0144e5916f" />

**CloudscribeNG** is a community-driven continuation of the original **cloudscribe** open-source project — a mature, modular ASP.NET Core library ecosystem covering logging, database abstraction, content management, web components, and more.

## Why CloudscribeNG Exists

**cloudscribe** represented years of careful, community-driven development across dozens of repositories. In June 2026, the original project's maintainers announced a move to closed-source licensing for future releases. That decision would have left the last open-source release frozen in time, with no path for the community to keep fixing bugs, adding features, or tracking the .NET platform forward.

Rather than let that body of work stagnate, **CloudscribeNG was created as a fork of the final open-source release of cloudscribe** — preserving all of its existing functionality while establishing a clear, independent path for continued open-source development. The name is deliberately a "strong, future-focused successor": it keeps the **cloudscribe** lineage recognizable, while clearly signalling that this is a new generation, not just a maintenance branch of the original.

## A Clean Break: Full Rename Across the Estate

To make CloudscribeNG safe to adopt alongside (or instead of) the original packages, **every repository, project, file, and namespace across the entire estate has been renamed** from `cloudscribe.*` to `CloudscribeNG.*`.

This was a deliberate, estate-wide decision, not just cosmetic branding:

- **No NuGet collisions.** The original `cloudscribe.*` packages still exist and are still installed in countless projects. Publishing packages under the same package IDs and namespaces would risk version conflicts, ambiguous upgrades, and broken builds for anyone consuming both.
- **No namespace collisions.** Renaming `namespace cloudscribe.*` to `namespace CloudscribeNG.*` (and updating every `using` statement, project reference, and solution file to match) means CloudscribeNG can be referenced side-by-side with legacy cloudscribe packages without type or assembly clashes.
- **Consistency across the board.** Folder names, `.csproj` files, `.sln` references, workspace configuration, and even code attribution strings were all updated together, so the estate is internally consistent rather than a patchwork of renamed and un-renamed pieces.
