**NOTE: WIP**

Harvester issues are managed with the GitHub project [Harvester Sprint](https://github.com/orgs/harvester/projects/7).

## Issue status

- **New**
  - A newly created issue goes here.
  - When creating an issue, you should
    - Select the correct issue **type**.
    - Add **labels** to the issue.
      - `kind/*`, `severity/*`, `area/*`, `reproduce/*` are required.
      - `priority/*`
        - `priority/0` for regression.
        - `priority/0` for customer issues first.
      - `backport-needed/*`: the issue needs to be backported to a specific version.
      - `require/ui-*`: the issue needs UI changes. For pure UI issues without backend change, please use `area/ui` label.
      - `require/doc`: the issue needs a document change. Please leave a comment on how we want the doc made.
      - `require/release-note`: the issue needs to be mentioned in the release note. Please add a comment on why we need to mention it.
      - `not-require/release-note`: the issue will not be listed in the release note. Suitable for CI or chore changes.
      - `not-require/test-plan`: don't create an issue in the `harvester/tests` repo.
    - Add a **milestone** only when:
      - You are a QA and the bug is a regression. Or the bug is critical and needs to be included in a milestone.
      - You are a PM or maintainer and decide to include the issue in a milestone.
      - Add the label `candidate/vx.y.z` if you wish to include this issue in a milestone.