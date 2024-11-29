**NOTE: WIP**

Harvester issues are managed with the GitHub project [Harvester Sprint](https://github.com/orgs/harvester/projects/7).

## Issue status

- **New**
  - A newly created issue goes here.
  - When creating an issue, you should
    - Select the correct issue type.
    - Add labels to the issue.
      - `kind/*`, `severity/*`, `area/*`, `reproduce/*` are required.
      - `priority/*`
        - `priority/0` for regression.
        - `priority/0` for customer issues first.
      - `backport-needed/*`: if the issue needs to backport to a specific version.
      - `require-ui/*`: if the issue needs some UI changes. For pure UI issues without backend change, please use `area/ui` label.
    - Add a milestone only when:
      - You are a QA and the bug is a regression. Or the bug is critical and needs to be included in a milestone.
      - You are a PM or maintainer and decide to include the issue in a milestone.
      - Add the label `candiate/vx.y.z` if you wish to include this issue in a milestone.