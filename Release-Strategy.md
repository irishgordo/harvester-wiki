Starting with Harvester 1.5, the release cadence and component dependencies will adhere to the following guidelines.

## Release Types

### Community Release

**x.y.z** release is considered a community release. **x.y** community support ends when version **x.(y+1).1** is released.

### Prime Release

**x.y.[1-z]** release is classified as a prime release and follows the support lifecycle of 6 months of active support followed by 12 months of maintenance support (ex: critical or CVE issues).

## Release Cadence

* 4-month cadence
* March (3), July (7), November (11)
* Patch releases will follow a 1.5-month cadence on a best-effort basis

## Dependency Adoption Cadence

### Dependency Release Cadence

* RKE2: April (4), August (8), December (12)
  * Follows the same release cadence as upstream Kubernetes.
  * Has a 14-month support period: 12 months of active support followed by 2 months of maintenance support.

* Longhorn: January (1), May (5), September (9)
  * Has 18-month support period since 1.5.

* Rancher: March (3), July(7), November (11) 
  * Starting with version 2.9, there is an 18-month support period.
  * Support RKE2 versions from version n-1 to n-4. (n means the upcoming/latest version)
  * Has an 18-month support period since 2.9.

* Harvester: March (3), July(7), November (11) 
  * Starting with version 1.5, there is an 18-month support period.

* KubeVirt: March (3), July(7), November (11) 
  * Support the recent three Kubernetes releases. For example, KubeVirt 1.4 supports K8s 1.31, 1.30, and 1.29, as K8s 1.33 is released after 1.4.
  * [KubeVirt Support Matrix](https://github.com/kubevirt/sig-release/blob/main/releases/k8s-support-matrix.md)
  * Has a 14-month support period aligning with the Kubernetes support period.

* SLE Micro: Biannual release
  * Released in the first and second half of the year.
  * Has a 4-year support period.

### Adoption Strategy

To ensure dependencies are supported during the Harvester release support period, adopt the following strategies using best efforts:

For components released before the Harvester release, use the latest release, **n**. 
* Longhorn

For components released after the Harvester release, use the **n-1** release instead.
* RKE2
* Rancher
* KubeVirt
* SLE Micro





