# Branches overview

## [harvester/harvester](https://github.com/harvester/harvester/branches)

The harvester main repo has these branch types:

- **Master branch** (`master`)

  The main branch. Every change for features, enhancements, and bug fixes has to be merged into the `master` branch first and be backported to stable branches.

- **Stable branches** (ex: `v1.4`, `v1.3`)

  A stable branch contains changes for a specific minor release. Bug fixes or enhancements must first be contributed to the `master` branch and then backported to a stable branch.

- **Release branches** (ex: `release/v1.4.0`, `release/v1.3.2`, `release/v1.4-dev-xxxx`)

  A release branch is used for cutting a formal release, release candidate, or a sprint release; it is checked out from a stable branch. For example, the `release/v1.4.0` branch is checked out from the `v1.4` branch. We add some useful commits, and then create a tag to publish the release.

## Dependency charts and add-ons

Harvester has many dependencies:

- [Dependency charts](https://github.com/harvester/harvester/blob/d3f5e936313b9530f5ff7d6cdc4572d60fd62555/deploy/charts/harvester/Chart.yaml#L14)
  - Harvester-maintained charts: `harvester-network-controller`, `harvester-node-manager`, `harvester-node-disk-manager`, etc. The charts live at https://github.com/harvester/charts, and each dependency has a corresponding repository to host its code and publish images.
  - Some charts are maintained by other organizations. For example, `longhorn`, `kube-vip`, `whereabouts`, etc.
  - The dependency charts's values are overridden by the Harvester [chart's values file](https://github.com/harvester/harvester/blob/master/deploy/charts/harvester/values.yaml).

- [Add-ons](https://github.com/harvester/addons/blob/main/version_info)
  - Charts live at https://github.com/harvester/charts.

### Versioning

Starting from Harvester v1.5.0, we want to sync the Harvester-maintained dependencies' versions with the Harvester version. Each dependency in Harvester v1.5.0 must versioned as v1.5.0 for both charts and image tags (except for harvester-cloud-provider and harvester-csi-driver as they are not direct dependency of Harvester and have different release cadence).

Here is the workflow:

- When we start working on a new milestone `v1.x.0`. Each dependency should create a `v1.x` branch. Codes for the minor releases must be backported to the branch.
- Sprint releases (before the first RC `v1.x.y-rc1`)
  - Each dependency publishes images with the tag like `v1.x.y-dev.0` (and `v1.x.y-dev.1` so on). The release is on demand.
  - Each dependency publishes charts with version lie  `v1.x.y-dev.0` (and `v1.x.y-dev.1` so on). The release is on demand.
  - The dependency chart owner must update the [dependency charts](https://github.com/harvester/harvester/blob/master/deploy/charts/harvester/Chart.yaml) and image tags in the [`values.yaml` files](https://github.com/harvester/harvester/blob/master/deploy/charts/harvester/values.yaml) in the harvester/harvester repo. The operation must be done on the master and stable branches.
  - The add-on owner must update the add-on chart and image tag in the [`addons repo`](https://github.com/harvester/addons/blob/main/version_info). The operation must be done on the main and stable branches.
- RC releases: nothing needs to be done here. The `v1.x.y-dev.z` versions are OK.
- After the first RC release and formal releases: use the same strategy in sprint releases.
- Formal release: the release captain must initiate an automatic task (TO ADD) to
  - Bump all dependencies/add-ons charts' version to `v1.x.y` and confirm charts are published.
  - Bump all dependencies/add-ons charts' image tags to `v1.x.y` and confirm images are published.
  - Update dependency charts and image tags in the `values.yaml` file in the master/stable branch (harvester/harvester).
  - Update dependency charts and image tags in the `version_info` file in the main/stable branch (harvester/addons).
  


 