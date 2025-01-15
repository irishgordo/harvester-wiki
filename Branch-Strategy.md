# Branches overview

## [harvester/harvester](https://github.com/harvester/harvester/branches)

The harvester main repo has these branch types:

- **Master branch** (`master`): The main branch. Every change for new features, bug fixes, and enhancements, has to be merged into the `master` branch first and be backported to stable branches.
- **Stable branches** (`v1.4`, `v1.3`, etc): A stable branch contains changes for a specific minor release. Bug fixes or enhancements must be first contributed to the `master` branch and then backported to a stable branch.
- **Release branches** (`release/v1.4.0`, `release/v1.3.2`, `release/v1.4-dev-xxxx`): Used for cutting a formal release, release candidate, or a sprint release. A release branch is checked out from a stable branch. For example, the `release/v1.4.0` branch is checked out from the `v1.4` branch. And we add some useful commits and then do a tag to publish a release.


## Dependency charts and add-ons

!!!!! ~~~~~ WIP WIP WIP ~~~~ !!!!!

Harvester has many dependencies:
- [Dependency charts](https://github.com/harvester/harvester/blob/d3f5e936313b9530f5ff7d6cdc4572d60fd62555/deploy/charts/harvester/Chart.yaml#L14)
  - The Harvester team maintains some charts: `harvester-network-controller`, `harvester-node-manager`, `harvester-node-disk-manager`, etc. The charts live at https://github.com/harvester/charts, and each dependency has a corresponding repository to host its code and publish images.
  - Some charts are maintained by other organizations. For example, `longhorn`, `kube-vip`, `whereabouts`, etc.
  - The dependency charts's values are overridden by the Harvester [chart's values file](https://github.com/harvester/harvester/blob/master/deploy/charts/harvester/values.yaml).
- [Add-ons](https://github.com/harvester/addons/blob/main/version_info)
  - Charts live at https://github.com/harvester/charts.

Starting from Harvester v1.5.0, we want to sync the dependencies' versions with the Harvester version. Note the dependencies here are components controlled by the Harvester team.

Here is the proposed workflow:


 