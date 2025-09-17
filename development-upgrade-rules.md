### Origin:
- [@starbops original guide on The Ultimate Policy of Harvester Upgrade](https://github.com/harvester/harvester/issues/2877#issuecomment-2042176079)


# The Ultimate Policy of Harvester Upgrade

## Glossary

- Formal release versions: a semantic version with no suffices, e.g. v1.2.2 and v1.3.0
- Prerelease versions: a semantic version with a suffix, e.g. v1.2.2-rc1
- Dev versions: any version that falls outside of the two above, e.g. v1.2-ab12cd34
- Minimum upgrade requirements: each formal release/prerelease version will incorporate an upgrade matrix file specifying the minimal upgradable version for such a version

## Rules

Since there are various upgrade paths, we explicitly enforce a set of rules to prohibit specific upgrade paths to ensure the stability of Harvester upgrades and reduce the maintenance burden for the Harvester team:

- Users are not allowed to upgrade from any version to a lower version (effectively downgrade)
- Users are not allowed to upgrade from any formal release version lower than the minimal upgrade requirement of the targeting formal release version
- Users are not allowed to upgrade from any dev version to a formal release version[^1]
- Users are not allowed to upgrade from any dev version to a prerelease version[^1]
- Users are allowed to upgrade from any lower prerelease version to a higher one within the same release version but not across different release versions

Upgrade paths other than the ones stated above are all legal. As a side note, we listed several peculiar upgrade paths that seem illegal but are actually doable below:

- Same-version upgrades are always allowed, no matter what the version is
- Upgrade from a formal release or prerelease version to a dev version is always allowed
- Upgrade from any dev version to another dev version is always allowed
- Upgrade from any prerelease version to a corresponding formal release version or another newer prerelease version within the same release version, e.g. v1.2.2-rc1 to v1.2.2-rc2

[^1]: By default, the strict mode is enabled to prevent any informal upgrades from happening. It could be disabled to loosen such a restriction.

Please feel free to add comments if the above rules are incomplete or need improvement. Thank you. cc @bk201  @khushboo-rancher 
