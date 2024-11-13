## Introduction

There are three types of Harvester release:

- Sprint release, e.g.: `v1.4.0-dev-20240916`.  This is a snapshot of
  the current state of development on that date.
- Release candidate, e.g.: `v1.4.0-rc1`.  This is what we plan to turn
  into a formal release, assuming it's feature complete and nobody finds any
  showstopper bugs.
- Formal release, e.g.: `v1.4.0`.  This is complete and ready for general use.

The procedure for creating a release of any type is essentially the same,
with some extra steps necessary for formal releases.  As Harvester consists
of many components, each of these need to be tagged, then all are combined
into a given release.  In brief, the release procedure is:

1. Check underyling component versions (node-disk-manager, etc.)
2. Bump [os2](https://github.com/harvester/os2) if necessary to include updated system packages
3. Tag [addons](https://github.com/harvester/addons)
4. Tag [harvester-installer](https://github.com/harvester/harvester-installer)
5. Tag [dashboard](https://github.com/harvester/dashboard)
6. Create a new [harvester](https://github.com/harvester/harvester) release branch
7. Create a release PR on that branch with an extra commit to tie the addons,
   harvester-installer, and dashboard tags to this release
8. _Formal releases only:_ Get QA sign-off, create release notes PR, update docs
9. Tag the harvester release
10. Wait for ISO build then add release notes
11. _Formal releases only:_ Update support matrix and upgrade responder

When creating sprint releases and release candidates, be sure to check the
"Set as a pre-release" box on the GitHub release page.  For formal releases,
check the "Set as the latest release" box, provided it really _is_ the latest
release (e.g. after we release v1.4.0 and set it as the latest release, if we
subsequently release v1.3.3, we would _not_ set that as the latest release,
because we want to encourage new users to use v1.4.0, rather than something
from the v1.3.x series).

Full details of the release procedure follow below.  There is a fair amount of
repetition in several of the steps.  While this makes the document longer that
it might strictly need to be, it should mean you can work through all the steps
one after another without having to refer back to generic instructions and
then potentially accidentally skip something.

## Release Procedure in Detail

Note: the various `git` command examples below assume you have remotes set
up so that "upstream" refers to a repository in the harvester organisation
and that "origin" refers to your personal fork.

1. Check underlying component versions (node-disk-manager, etc.)
   - Updates to these generally happen as part of regular daily development
     and changes are merged into the [stable branches](Branch-Strategy).
   - Important files in the [harvester](https://github.com/harvester/harvester) repo are:
     - `deploy/charts/harvester/Chart.{yaml,lock}` (chart versions)
     - `deploy/charts/harvester/charts/*tgz` (chart tarballs)
     - `deploy/charts/harvester/values.yaml` (image tags)
   - A good example of a stable branch PR which updates all of the above is
     https://github.com/harvester/harvester/pull/6925
   - If any underlying component has important changes for this release, but
     the harvester repo is still using an older version, work with the component owner to update the charts and image tags appropriately.

2. Bump `os2` if necessary to include updated system packages:
   - Check the "Package differences" actions to see if there are new package
     updates: https://github.com/harvester/os2/actions/workflows/diff.yml
   - The diffs are in output of the "Run container-diff" step of the various
     "fetch-*-diff" jobs, e.g. for v1.4.x, check "fetch-v1-4-x-diff".
   - If there are new packages, tag a new version as `vMAJOR.MINOR-YYYYMMDD`
     in the appropriate branch.  For example
     [v1.4-20241105](https://github.com/harvester/os2/releases/tag/v1.4-20241105)
     was tagged on the harvester-v1.4.x branch.
   - Tagging os2 will automatically trigger the
     [installer-pr](https://github.com/harvester/os2/actions/workflows/installer-pr.yml)
     workflow which will open PRs against stable branches of
     [addons](https://github.com/harvester/addons/)
     and [harvester-installer](https://github.com/harvester/harvester-installer),
     for example:
       - https://github.com/harvester/addons/pull/32
       - https://github.com/harvester/harvester-installer/pull/877
   - Make sure to edit the os2 release notes to link to these PRs (see any
     of the existing releases for examples).
   - The addons and harvester-installer PRs must be reviewed and merged
     before proceeding to the next step.

3. Tag `addons`:
   - Check for open PRs targeting the stable branch we are about to tag.
     For example, for v1.4.x, search for
     [`is:pr is:open base:v1.4`](https://github.com/harvester/addons/pulls?q=is%3Apr+is%3Aopen+base%3Av1.4)
     - If there are any PRs open that we care about for this release, get
       them merged.
   - Use `git cherry -v` to verify there's no commits in the main branch that
     still need to be backported to the stable branch, and vice versa.
     If anything is wrong or missing, fix it.
     ```shell
     ~/harvester/addons> git fetch upstream

     ~/harvester/addons> git cherry -v upstream/v1.4 upstream/main | grep '^+'
     # this shows commits that are in main but (possibly) not in v1.4

     ~/harvester/addons> git cherry -v upstream/main upstream/v1.4 | grep '^+'
     # this shows commits that are in v1.4 but (possibly) not in main
     ```
   - [Tag the release](https://github.com/harvester/addons/releases/new)
     targeting the appropriate stable branch:
     - For sprint releases, use `vMAJOR.MINOR.PATCH-dev-YYYYMMDD`, e.g.: `v1.4.0-dev-20240916` on the `v1.4` branch.
     - For release candidates, use `vMAJOR.MINOR.PATCH-rcN`, e.g.: `v1.4.0-rc1` on the `v1.4` branch.
     - For the final release, use `vMAJOR.MINOR.PATCH`, e.g.: `v1.4.0` on the `v1.4` branch.

4. Tag `harvester-installer`:
   - Check for open PRs targeting the stable branch we are about to tag.
     For example, for v1.4.x, search for
     [`is:pr is:open base:v1.4`](https://github.com/harvester/harvester-installer/pulls?q=is%3Apr+is%3Aopen+base%3Av1.4)
     - If there are any PRs open that we care about for this release, get
       them merged.
   - Use `git cherry -v` to verify there's no commits in the master branch that
     still need to be backported to the stable branch, and vice versa.
     If anything is wrong or missing, fix it.
     ```shell
     ~/harvester/harvester-installer> git fetch upstream

     ~/harvester/harvester-installer> git cherry -v upstream/v1.4 upstream/master | grep '^+'
     # this shows commits that are in master but (possibly) not in v1.4

     ~/harvester/harvester-installer> git cherry -v upstream/master upstream/v1.4 | grep '^+'
     # this shows commits that are in v1.4 but (possibly) not in master
     ```
   - [Tag the release](https://github.com/harvester/harvester-installer/releases/new)
     targeting the appropriate stable branch:
     - For sprint releases, use `vMAJOR.MINOR.PATCH-dev-YYYYMMDD`, e.g.: `v1.4.0-dev-20240916` on the `v1.4` branch.
     - For release candidates, use `vMAJOR.MINOR.PATCH-rcN`, e.g.: `v1.4.0-rc1` on the `v1.4` branch.
     - For the final release, use `vMAJOR.MINOR.PATCH`, e.g.: `v1.4.0` on the `v1.4` branch.

5. Tag `dashboard`:
   - Check for open PRs targeting the stable branch we are about to tag.
     For example, for v1.4.x, search for
     [`is:pr is:open base:release-harvester-v1.4`](https://github.com/harvester/dashboard/pulls?q=is%3Apr+is%3Aopen+base%3Arelease-harvester-v1.4)
     - If there are any PRs open that we care about for this release,
       work with the UI team to get them merged.
   - Use `git cherry -v` to verify there's no commits in the master branch that
     still need to be backported to the stable branch, and vice versa.
     If anything is wrong or missing, work with the UI team to fix it.
     ```shell
     ~/harvester/dashboard> git fetch upstream

     ~/harvester/dashboard> git cherry -v upstream/release-harvester-v1.4 upstream/master | grep '^+'
     # this shows commits that are in master but (possibly) not in release-harvester-v1.4

     ~/harvester/dashboard> git cherry -v upstream/master upstream/release-harvester-v1.4 | grep '^+'
     # this shows commits that are in release-harvester-v1.4 but (possibly) not in master
     ```
   - Ask the UI team leader to
     [tag the release](https://github.com/harvester/dashboard/releases/new)
     targeting the appropriate stable branch:
     - For sprint releases, use `vMAJOR.MINOR.PATCH-dev-YYYYMMDD`, e.g.: `v1.4.0-dev-20240916` on the `v1.4` branch.
     - For release candidates, use `vMAJOR.MINOR.PATCH-rcN`, e.g.: `v1.4.0-rc1` on the `v1.4` branch.
     - For the final release, use `vMAJOR.MINOR.PATCH`, e.g.: `v1.4.0` on the `v1.4` branch.

6. Create a new `harvester` release branch:
   - Check for open PRs targeting the stable branch we are about to tag.
     For example, for v1.4.x, search for
     [`is:pr is:open base:v1.4`](https://github.com/harvester/harvester/pulls?q=is%3Apr+is%3Aopen+base%3Av1.4)
     - If there are any PRs open that we care about for this release, get
       them merged.
   - Use `git cherry -v` to verify there's no commits in the master branch that
     still need to be backported to the stable branch, and vice versa.
     If anything is wrong or missing, fix it.
     ```shell
     ~/harvester/harvester> git fetch upstream

     ~/harvester/harvester> git cherry -v upstream/v1.4 upstream/master | grep '^+'
     # this shows commits that are in master but (possibly) not in v1.4

     ~/harvester/harvester> git cherry -v upstream/master upstream/v1.4 | grep '^+'
     # this shows commits that are in v1.4 but (possibly) not in master
     ```
   - Create the release branch forked from the appropriate stable branch
     - For sprint releases, use `release-vMAJOR.MINOR.PATCH-dev-YYYYMMDD`, e.g.: `release-v1.4.0-dev-20240916` forked from the `v1.4` branch.
     - For release candidates, use `release-vMAJOR.MINOR.PATCH-rcN`, e.g.: `release-v1.4.0-rc1` forked from the `v1.4` branch.
     - For the final release, use `release-vMAJOR.MINOR.PATCH`, e.g.: `release-v1.4.0` forked from the `v1.4` branch.
     - Example:
       ```shell
       ~/harvester/harvester> git fetch upstream
       ~/harvester/harvester> git checkout -b release-v1.4.0-rc1 upstream/v1.4
       ~/harvester/harvester> git push --set-upstream upstream release-v1.4.0-rc1
       ```

7. Create a release PR based on the release branch from the previous step:
   - We need to add one more commit which updates the UI version, installer
     version, addons version, and upgrade matrix.
   - Example:
     ```shell
     ~/harvester/harvester> git fetch upstream
     ~/harvester/harvester> git checkout -b wip-v1.4.0-rc1 upstream/release-v1.4.0-rc1
     ~/harvester/harvester> vim package/Dockerfile \
                                package/upgrade-matrix.yaml \
                                scripts/build-iso \
                                scripts/generate-addons
     # Make appropriate changes to:
     # - HARVESTER_UI_VERSION
     # - HARVESTER_UI_PLUGIN_BUNDLED_VERSION
     # - HARVESTER_INSTALLER_VERSION
     # - HARVESTER_ADDONS_VERSION
     # - list of versions in upgrade matrix
     ~/harvester/harvester> git commit -sm 'Release v1.4.0-rc1' \
                                package/Dockerfile \
                                package/upgrade-matrix.yaml \
                                scripts/build-iso \
                                scripts/generate-addons
     ~/harvester/harvester> git push --set-upstream origin wip-v1.4.0-rc1
     ```
   - Then open a PR targeting the release branch, for example:
     https://github.com/harvester/harvester/pull/6956
   - Run smoke tests on the PR:
     - https://github.com/bk201/harvester-works/actions/workflows/test-smoke-harvester.yml
     - Use parameters:
       - harvester_prs: your release PR, e.g.: https://github.com/harvester/harvester/pull/6956
       - extra_run_label: lab
       - harvester_tests_repo: https://github.com/bk201/tests
       - harvester_tests_repo_branch: dev-smoke
   - The smoke tests will take about two hours.  Once complete, review the
     results, and if they appear acceptable, upload the results zip file in
     a comment on the release PR.
   - Get the release PR reviewed and merged.
8. _Formal releases only:_ TODO: fill this in properly
   - Create a release notes PR
   - Get signoff from QA
   - Create upgrade page
   - Update README
9. Tag the release on the release branch:
   - Example:
     ```shell
     ~/harvester/harvester> git fetch upstream
     ~/harvester/harvester> git tag -a v1.4.0-rc1 upstream/release-v1.4.0-rc1 -m 'Tag Harvester v1.4.0-rc1'
     ~/harvester/harvester> git push upstream v1.4.0-rc1
     ```
   - Tagging the release will trigger CI to build and publish ISO images.  This will
     also create a draft release on GitHub for the version just tagged.
10. Add release notes to the GitHub release page.
11. _Formal releases only:_ TODO: fill this in properly
    - Update support matrix and upgrade responde

## How to Generate / Write Release Notes

TODO: write this section

## Everything You Ever Wanted to Know About `git cherry -v` but Were Afraid to Ask

TODO: 
- Explain how this is the most useful `git` command you will ever encounter
  when working with multiple release branches
- Think of a less silly title for this section of the document