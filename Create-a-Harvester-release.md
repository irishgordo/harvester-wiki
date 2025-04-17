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
     # - HARVESTER_INSTALLER_REF
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
     - https://github.com/harvester/release/actions/workflows/test-smoke-harvester-pr.yml
     - Use parameters:
       - harvester_prs: your release PR, e.g.: https://github.com/harvester/harvester/pull/6956
       - extra_run_label: lab
       - harvester_tests_repo: https://github.com/bk201/tests
       - harvester_tests_repo_branch: dev-smoke
   - The smoke tests will take about two hours.  Once complete, review the
     results, and if they appear acceptable, upload the results zip file in
     a comment on the release PR.
   - Get the release PR reviewed and merged.
8. _Formal releases only:_
   - Create a release notes PR in https://github.com/harvester/release-notes/pulls, for instance: https://github.com/harvester/release-notes/pull/35
     - Check if there's a "New contributor issue" in https://github.com/harvester/release-notes/issues
     - Remove unused contributors
     - Update known issues and components
     - cc project owner and technical writer to review
   - Get signoff from QA
   - Create upgrade page for the release
     - e.g. https://docs.harvesterhci.io/v1.3/upgrade/v1-2-2-to-v1-3-1
   - Update README
     - Update versions: https://github.com/harvester/harvester?tab=readme-ov-file#releases
     - Update screenshots
   - See https://github.com/harvester/harvester/issues/6969 for an example of the above using the v1.4.0 release.
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
11. _Formal releases only:_ (TODO: confirm/verify these steps)
    - Update supported-versions (for security scanning)
      - https://github.com/harvester/harvester/blob/master/misc/support-versions.txt
    - Update support matrix
    - Publish a new version to the upgrade responder a few days after
      the release (the delay is just in case anyone reports a critical
      issue), you can check https://SUSE_INTERNAL_CONFLUENCE/display/HARV/Harvester+upgrade+responder

## How to Generate / Write Release Notes

Use the `harvester-release` tool from https://github.com/harvester/release

See https://github.com/harvester/release/tree/main/docs for details.

TODO: fill in details

## How to Use `git cherry -v` Effectively

The `git-cherry` man page starts with the following description:

 > ```
 > NAME
 >        git-cherry - Find commits yet to be applied to upstream
 >
 > SYNOPSIS
 >        git cherry [-v] [<upstream> [<head> [<limit>]]]
 >
 > DESCRIPTION
 >        Determine whether there are commits in <head>..<upstream>
 >        that are equivalent to those in the range <limit>..<head>.
 > ```

It goes on to provide some usage examples, but these can be a bit
confusing in some respects.  A simpler way to think about it is to
not use the `upstream` and `head` terminology, but instead refer to
those as branch `A` and branch `B`.  At some point in the past, those
two branches forked, i.e. they have a common ancestor, but since then
they have potentially diffent set of commits.

`git cherry -v A B` looks at both branches after the fork, and tells us:

- The commits in `B` for which equivalent commits exist in `A`,
  prefixed with a `-` symbol
- The commits that are only in `B` prefixed with a `+` symbol.

This is very helpful when trying to figure out what commits may still
need to be backported from a repo's `master` or `main` branch to a
stable release branch (e.g. `v1.4`), and vice versa in case there are
somehow changes in a stable branch that might need forward porting to
`master` or `main`.

Here's an example using the `harvester-installer` repo:

```shell
~/harvester/harvester-installer> git cherry -v --abbrev=8 upstream/v1.4 upstream/master
+ c5abc939 build: minor rewording, fix harvester_path consistency
+ 9ea63a0c build: Add LOCAL_{ADDONS,HARVESTER}_SRC arguments
- e7671672 Bump golang to v1.22
- 36ea2bf0 vendor: move `mudler/yip` to `rancher/yip`
- 5ffebedd Log validateConfig, PrepareWebooks and doInstall errors
- 24a8e733 Add addon version check with the repo
[...snipped many more lines of output...]
```

In the above output we can see that the first two commits (`c5abc939` and
`9ea63a0c`) are prefixed with a `+` symbol, meaning they have been
applied to `master` but haven't been backported to `v1.4`, whereas
the following four commits exist in both branches.

Now let's try the reverse:

```shell
~/harvester/harvester-installer> git cherry -v --abbrev=8 upstream/master upstream/v1.4
+ c3f79c03 Bump OS v1.4-20240705
+ 99f7bd20 Move harvester and add-on repos to the v1.4 branches
- f872a10c Bump golang to v1.22
+ a1c2aec4 Bump OS v1.4-20240719
- bd82c0ef Log validateConfig, PrepareWebooks and doInstall errors
- 3b8fad60 vendor: move `mudler/yip` to `rancher/yip`
[...snipped many more lines of output...]
```

In the above output we can see there's three commits applied to the `v1.4`
branch that aren't in `master`, and three that are.  Note how the three
commits that are applied to both branches have the same description as in
the earlier output, just with different commit hashes.

At this point, we need to figure out whether anything needs to be back-
or forwards-ported.  For this purpose we can ignore all the lines prefixed
with `-`.  Here's a complete example using harvester-installer:

```shell
~/harvester/harvester-installer> git cherry -v --abbrev=8 upstream/v1.4 upstream/master | grep '^+'
+ c5abc939 build: minor rewording, fix harvester_path consistency
+ 9ea63a0c build: Add LOCAL_{ADDONS,HARVESTER}_SRC arguments
+ 8d366c52 Bump depended dhcp client package
+ 677447c8 leverage generateTemplates capability of harvester/addons
+ e80ee9a7 Update monitoring and logging addon with template generation (#792)
+ f0b21b99 allow installation to external disks, configure multipathd and extra kernel arguments
+ 1a33e22b chore: Configure Mergify to open backport PRs by labels [skip ci]

~/harvester/harvester-installer> git cherry -v --abbrev=8 upstream/master upstream/v1.4 | grep '^+'
+ c3f79c03 Bump OS v1.4-20240705
+ 99f7bd20 Move harvester and add-on repos to the v1.4 branches
+ a1c2aec4 Bump OS v1.4-20240719
+ e148c838 leverage generateTemplates capability of harvester/addons
+ 6f96c053 Bump OS v1.4-20240801
+ 95b72007 Update monitoring and logging addon with template generation (#792)
+ c240c652 allow installation to external disks, configure multipathd and extra kernel arguments
+ 3f9513b7 Bump OS v1.4-20240830
+ 00d3a70a Bump OS v1.4-20240916
+ 467a3ca5 Bump OS v1.4-20240923
+ e7aa395f Bump OS v1.4-20241001
+ 5b619437 Bump OS v1.4-20241009
+ 5797d309 Bump OS v1.4-20241016
+ d65a8a8b Bump OS v1.4-20241023
+ 42fc086f Bump OS v1.4-20241105
```

In the case of harvester-installer, "Bump OS" commits are only ever applied
to stable branches (`v1.4` in this example), so we don't care that they're
not in `master`.  Likewise the "Move harvester and add-on repos to the v1.4
branches" commit.

The two build-related commits are fine to only exist in `master`, as they're
not important for the v1.4 release.  It wouldn't _hurt_ to backport them,
but there's really no need.

The "Bump depended dhcp client package" commit is from
<https://github.com/harvester/harvester-installer/pull/779>. At the time
of writing this is part of some ongoing work in
https://github.com/harvester/harvester-installer/pull/767 which is why
it was never backported to v1.4.

The remaining three commits ("leverage generateTemplates capability of
harvester/addons", "Update monitoring and logging addon with template
generation" and "allow installation to external disks, configure
multipathd and extra kernel arguments") are interesting, because those
look like things that we want in both branches.  Why does `git cherry`
tell us that each branch has a commit that isn't in the other branch,
but with matching descriptions?  This happens when there's some
subtle difference in the code necessary between the two branches which
means that the commits are not quite identical.  It can be instructive
to experiment with commands like
`diff -u <(git show 677447c8) <(git show e148c838)` to view the
differences between two commits with the same description.  In the
case of the three interesting commits here, the differences are due to
branch names and dependency versions, so not something we need to worry
about.

Similar oddities can also show up when multiple commits are made in one
branch, but are then squashed into a single commit when backporting to
another branch.  In this case it's a matter of double checking that all
the right changes made it in by looking at and comparing the individual
commits, or sets of commits.