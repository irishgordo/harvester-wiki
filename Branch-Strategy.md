# Branches overview

## [harvester/harvester](https://github.com/harvester/harvester/branches)

The harvester main repo has these branch types:

- **Master branch** (`master`): The main branch. Every change for new features, bug fixes, and enhancements, has to be merged into the `master` branch first and be backported to stable branches.
- **Stable branches** (`v1.4`, `v1.3`, etc): A stable branch contains changes for a specific minor release. Bug fixes or enhancements must be first contributed to the `master` branch and then backported to a stable branch.
- **Release branches** (`release/v1.4.0`, `release/v1.3.2`, `release/v1.4-dev-xxxx`): Used for cutting a formal release, release candidate, or a sprint release. A release branch is checked out from a stable branch. For example, the `release/v1.4.0` branch is checked out from the `v1.4` branch. And we add some useful commits and then do a tag to publish a release.