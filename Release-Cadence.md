Starting with Harvester 1.5, the release cadence and component dependencies will adhere to the following guidelines.

## Release Types

### Community Release

vX.Y.Z release is considered a community release. vX.Y community support ends when version vX.(Y+1).1 is released.

### Prime Release

vX.Y.(1-Z) release is classified as a prime release and follows the support lifecycle of 6 months of active support followed by 12 months of maintenance support (ex: critical or CVE issues).

## Release Cadence

* 4-month cadence
* Mar(3), Jul(7), Nov(11)
* Patch release will be a 1.5-month release cadence with best efforts

## Dependency Adoption Cadence

### Dependency Release Cadence

* RKE2: Apr(4) Aug(8) Dec(12) 
  * RKE2 is following the same release cadence as upstream K8s

* Longhorn: Jan(1) May(5) Sep(9)
  * From 1.8, 18 months support period

* Rancher: Mar(3) Jul(7) Nov(11) 
  * From 2.9, 18 months support period. 
  * Support RKE2 v<n-1> ~ v<n-4>

* Harvester: Mar(3) Jul(7) Nov(11) 
  * From 1.5, 18 months support period

* KubeVirt: Mar(3) Jul(7) Nov(11) 

* SLE Micro: Biannual release
  * First and second half-year release. 
  * 4 years maintenance period)

### Adoption Strategy

* Use

(RKE2 v<n-1>, Rancher v<n-1>, KubeVirt v<n-1>)






