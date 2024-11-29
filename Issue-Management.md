**NOTE: WIP**

Harvester issues are managed with the GitHub project [Harvester Sprint](https://github.com/orgs/harvester/projects/7).

## Issue status

Issues managed in the Harvester Sprint project have status. The statuses are described below and the roles in the parentheses mean who can move an issue to the status. Note this is a general guideline, if unsure please check with the PM and maintainers.

- **New Issues** (Everyone)
  - A newly created issue goes here.
  - When creating an issue, you should
    - Select the correct issue **type**.
    - Add **labels** to the issue.
      - `kind/*`, `severity/*`, `area/*`, and `reproduce/*` are required.
      - `priority/*`
        - `priority/0` for regression.
        - `priority/0` for customer issues first.
        - Maintainers will add a priority if the issue is moved to the backlog with some discussion.
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

- **Backlog** (Maintainer)
  - The issue will be included in the corresponding milestone.
  - If the feature or bug is complex, Add the `require/HEP` label.

- **Analysis/Design** (Assignee)
  - The assignee starts working on the issue.
  - Add a HEP and create a HEP discussion meeting if there is a `require/HEP` label.



- **Implement** (Assignee, QA)
  - The assignee starts working on the issue.

- **Review** (Assignee)
- **Icebox** (Maintainer, Assignee)
- **Ready For Testing** (Assignee)
- **Testing** (QA)
- **Closed** (QA, Maintainer, Assignee)

