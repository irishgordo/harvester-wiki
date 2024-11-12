TODO: Insert introductory paragraph here which explains:
  - assumptions about github remotes in examles (upstream == harvester)
  - need to add section (at the end?) explaining use of `git cherry -v` and caveats, things to look out for
  - notes about tagging releases and flagging them as prerelease, autogenerate release notes, etc.
  - There's a lot of repeated information in the steps below.  That's intentional, because even though it makes it look like this is a really long document, it means you can just work through it for each repo (os2, addons, harvester-installer, dashboard, harvester) without having to refer to generic instructions and then accidetnally miss a step ;-)

To create a harvester release:

1. Bump components (charts and image tags)
   - These generally happen in daily development and changes are merged
     into the [stable branches](Branch-Strategy).
   - TODO: describe specifically what to check here
2. Bump `os2`:
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
     # ...make appropriate changes...
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
     - TODO: insert link to smoke workflow
     - Use parameters:
       - harvester_prs: your release PR, e.g.: https://github.com/harvester/harvester/pull/6956
       - extra_run_label: lab
       - harvester_tests_repo: https://github.com/bk201/tests
       - harvester_tests_repo_branch: dev-smoke
   - The smoke tests will take about two hours.  Once complete, review the
     results, and if they appear acceptable, upload the results zip file in
     a comment on the release PR.
   - Get the release PR reviewed and merged.
8. TODO: insert steps here for formal releases:
   - Create a release notes PR
   - Get signoff from QA
   - Create upgrade page
   - Update README
9. Tag the release
   - TODO: fill this out
10. TODO: More steps for formal releases go here
     