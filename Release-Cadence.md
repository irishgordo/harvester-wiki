Starting with Harvester 1.5, the release cadence and component dependencies will adhere to the following guidelines.

## Release Types

### Community Release

vX.Y.Z release is considered a community release. vX.Y community support ends when version vX.(Y+1).1 is released.

### Prime Release

vX.Y.(1-Z) release is classified as a prime release and follows the support lifecycle of 6 months of active support followed by 12 months of maintenance support (ex: critical or CVE issues).

## Release Cadence

* 4-month cadence
* March (3), July (7), November (11)
* Patch releases will follow a 1.5-month cadence on a best-effort basis

## Dependency Adoption Cadence

### Dependency Release Cadence

* RKE2: April (4), August (8), December (12)
  * RKE2 follows the same release cadence as upstream Kubernetes (K8s).

* Longhorn: January (1), May (5), September (9)
  * Starting with version 1.8, there is an 18-month support period.

* Rancher: Mar(3) Jul(7) Nov(11) 
  * Starting with version 2.9, there is an 18-month support period.
  * Supports RKE2 versions from v<n-1> to v<n-4>.

* Harvester: Mar(3) Jul(7) Nov(11) 
  * Starting with version 1.5, there is an 18-month support period.

* KubeVirt: Mar(3) Jul(7) Nov(11) 

* SLE Micro: Biannual release
  * Released in the first and second half of the year.
  * Support a 4-year maintenance period.

### Adoption Strategy

* Use

(RKE2 v<n-1>, Rancher v<n-1>, KubeVirt v<n-1>)






