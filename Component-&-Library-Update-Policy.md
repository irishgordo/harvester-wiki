For each feature or minor release, the versions of the following components and libraries must be updated to a new minor version.

> [!Important]
> For each patch release, the versions of the following components and libraries must be updated to a new _**patch**_ version.

## Underlying Components 

> [!Note]
> Please check https://github.com/harvester/harvester/wiki/Release-Cadence-%26-Support#dependency-adoption-cadence to understand what release cadence of each component is.
>
> `N` refers to the latest minor version. The release cadence of RKE2 aligns with that of Harvester, so Harvester will adopt the most recent minor release instead of the latest one, which is released in the same month. 

* Longhorn: N
* Rancher (Embedded): N
* KubeVirt: N-1
* RKE2: N-1
* SLE Micro: N-1

## Primary Libraries and Langugage Runtimes

> [!Note]
> `N` refers to the latest minor version.

* Go version: N
* K8s libraries: N-1 (aligning with the version for the underlying RKE2)
* BCI base image: N
