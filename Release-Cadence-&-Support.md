Starting with Harvester 1.5, the release cadence and component dependencies will adhere to the following guidelines.

## Release Types

### Community Release

- **x.y.z** release is considered a community release. **x.y** community support ends when version **x.(y+1).1** is released.

### Prime Release

- **x.y.[1-z]** release is classified as a prime release and follows the support lifecycle of 6 months of active support followed by 12 months of maintenance support (ex: critical or CVE issues).

## Release Cadence

* 4-month minor release cadence. April, August, December
* 2-month patch release cadence with best efforts

## Dependency Adoption Cadence

### Dependency Release Cadence

* RKE2: April, August, December
  * Follows the same release cadence as upstream Kubernetes.
  * Has a 14-month support period: 12 months of active support followed by 2 months of maintenance support.

* Longhorn: January, May, September
  * Has an 18-month support period since 1.9.

* Rancher: March, July, November 
  * Starting with version 2.9, there is an 18-month support period.
  * Support RKE2 versions from version n-1 to n-4. (n means the upcoming/latest version)
  * Has an 18-month support period since 2.9.

* Harvester: April, August, December
  * Starting with version 1.5, there is an 18-month support period.

* KubeVirt: March, July, November
  * Support the recent three Kubernetes releases. For example, KubeVirt 1.4 supports K8s 1.31, 1.30, and 1.29, as K8s 1.33 is released after 1.4.
  * [KubeVirt Support Matrix](https://github.com/kubevirt/sig-release/blob/main/releases/k8s-support-matrix.md)
  * Has a 14-month support period aligning with the Kubernetes support period.

* SLE Micro: Biannual release
  * Released in the first and second half of the year.
  * Has a 4-year support period.

### Adoption Policy

To ensure dependencies are supported during the Harvester release support period, adopt the following strategies:

For components released before the Harvester release, use the latest minor release, **n**. 
* Longhorn
* Rancher

For components released after the Harvester release, use the **n-1** release instead.
* RKE2
* KubeVirt
* SLE Micro

## External Rancher Support

For external Rancher support, each Rancher version will be supported to manage the corresponding Harvester version released in the same month. When upgrading the managed Harvester clusters, users must upgrade Rancher first to the supported version, followed by Harvester upgrade.

When Rancher versions reach EOM (6 months) for active support, only critical and CVE issues are prioritized for fixing in the Harvester-Rancher integration functions (Virtualization Management).

### Support Matrix

- Harvester 1.3/1.4 -> Rancher 2.9 and 2.10
- Harvester 1.5 -> Rancher 2.11 (lifecycle alignment)
- Harvester 1.6 -> Rancher 2.12 (lifecycle alignment)
