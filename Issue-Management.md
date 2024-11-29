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
      - `require/HEP`: the issue needs a Harvester Enhancement Proposal first.
      - `require/doc`: the issue needs a document change. Please leave a comment on how we want the doc made.
      - `require/release-note`: the issue needs to be mentioned in the release note. Please add a comment on why we need to mention it.
      - `not-require/release-note`: the issue will not be listed in the release note. Suitable for CI or chore changes.
      - `not-require/test-plan`: don't create an issue in the `harvester/tests` repo.
    - Add a **milestone** only when:
      - You are a QA and the bug is a regression. Or the bug is critical and needs to be included in a milestone.
      - You are a PM or maintainer and decide to include the issue in a milestone.
      - Add the label `candidate/vx.y.z` if you wish to include this issue in a milestone.

- **Backlog** (Maintainer)
  - An issue with the status will be included in the corresponding milestone.
  - The issue must have a priority.
  - If the feature or bug is complex, Add the `require/HEP` label.

- **Analysis/Design** (Assignee)
  - The assignee starts analyzing an issue or designing a feature/enhancement.
  - [Add a HEP ](https://github.com/harvester/harvester/tree/master/enhancements)and create a HEP discussion meeting if there is a `require/HEP` label.


- **Implement** (Assignee, QA)
  - The assignee starts working on an issue.
  - If the issue has a `require/ui-*` label, the backend engineer needs to work with the UI engineer engineer. The UI engineer must update the corresponding UI issue accordingly.
  - If the issue has a `require/doc` label, the backend engineer needs to work with the technical writer to create a document.

- **Review** (Assignee)
  - The assignee to an issue finishes implementation and works (PRs) are ready for review.
  - If the issue has a `require/ui-*` label, its UI implementation needs to be ready for review too.
  - If the issue has a `require/doc` label, it's better to have a draft doc PR for review.
  - The assignee must fill in the "Pre Ready-For-Testing Checklist" in the PR ([example](https://github.com/harvester/harvester/issues/6618#issuecomment-2372714261)). The comment must have clear reproduction steps and a test plan.

- **Icebox** (Maintainer, Assignee)
  - An issue depends on an upstream or 3rd-party issue and can't proceed.

- **Ready For Testing** (Assignee)
  - The assignee can move an issue to the status if the issue is ready for testing. The assignee must double check there is a test plan in the "Pre Ready-For-Testing Checklist" comment.

- **Testing** (QA)
  - QA is verifying an issue.
  - QA must move an issue back to the **Implement** status if the issue validation fails.


- **Closed** (QA, Maintainer, Assignee)
  - An issue is validated and all related works (UI and doc) are finished.

