> [!Note]
> About each sprint project, please check [[Sprint Project Boards]]. 

We manage two types of issues from different sources:

- Community users - Managed via [Community Sprint - Issue Management](https://github.com/harvester/harvester/wiki/Community-Issue-Management)
- Harvester Team members - Managed via [Harvester Sprint - Issue Management](https://github.com/harvester/harvester/wiki/Harvester-Developer-Issue-Management)

If a community issue is well-defined and clarified, we add it to the Harvester Sprint for development.


## General Issue Flow

> [!Important]
> If the issue is addressed, please close it directly.

* When an issue is created:
  
  * If the creator is not a member of the organization, the issue is added to the [Community Issue Management](https://github.com/harvester/harvester/wiki/Community-Issue-Management).
  
  * If the creator is a member of the organization(harvester org), the issue is added to the [Harvester Developer Issue Management](https://github.com/harvester/harvester/wiki/Harvester-Developer-Issue-Management) with the default `Planning` milestone.
  
  * If the creator is a member of the organization(SUSE org), the issue is assigned with the `require/pm-review` label and added to [Product Backlog Triage](https://github.com/orgs/harvester/projects/2)

* Once an issue in [Community Issue Management](https://github.com/harvester/harvester/wiki/Community-Issue-Management) has sufficient information **for development**:
  
  * Update status to `Resolved` in [Community Issue Management](https://github.com/harvester/harvester/wiki/Community-Issue-Management).


## Issues From [Community Issue Review](https://github.com/orgs/harvester/projects/10) To [Harvester Sprint](https://github.com/orgs/harvester/projects/7)

```mermaid
flowchart LR
    A[Newly Created Issue] --> B{Who is creator?}
    B -- Community Users --> C[Added to Community Issue Review project]
    B -- Team Members --> G{Is milestone set?}
    
    G -- Yes --> D[Added to Harvester Sprint project]
    G -- No --> H[Add Planning milestone]
    H --> D

    C --> I[Discuss with users and team members]
    I --> J{Is it development-related?}
    J -- No, it's Q&A --> K[No milestone needed. Close it after answering]
    J -- Yes --> L[Set milestone]
    L --> D
```

## GitHub Action Workflows

> [!Important]
> The workflows described below may change over time. For the exact settings, please refer to the workflows defined in https://github.com/harvester/harvester/tree/master/.github/workflows/issue-management-*.yml.

### Issue Creation and Auto-Categorization

When a new issue is created, the system automatically categorizes it based on the creator’s identity:

- [Harvester Team Members](https://github.com/harvester/harvester/blob/master/.github/workflows/issue-management-update-harvester-projects.yaml)
- [Community Coordinators](https://github.com/harvester/harvester/blob/master/.github/workflows/issue-management-update-community-projects.yaml)

### [Sprint Cycle Management](https://github.com/harvester/harvester/blob/master/.github/workflows/issue-management-update-issue-sprint.yml)

Sprint updates are automatically executed every Sunday at 20:00.

Harvester Sprint (Project #7):
* Move issues with the “Review” status to the next sprint
* Remove issues from the current sprint if their status is not one of: Review, Ready For Testing, Testing, or Closed

Community Sprint (Project #10):

* Move issues with the “New” status to the next sprint

QA Sprint (Project #20):

* Move issues with the “In Review” status to the next sprint
* Remove issues from the current sprint if their status is not In Review or Done

### [Issue Stale Management](https://github.com/harvester/harvester/blob/master/.github/workflows/issue-management-stale.yml)

* Runs daily at 1:30 AM to check activity of issues and PRs
* Close unactivated issues and PRs


### [Automatically create issue by label](https://github.com/harvester/harvester/blob/master/.github/workflows/issue-management-create-issue-by-label.yaml)

There are three types automatic creation:

- Backport Issue:  
    The older versions might need to be patched due to CVEs or bug fixes. We'll use the `backport-needed/xxx` label to help us create the corresponding issue. Please check [Backport-Policy](https://github.com/harvester/harvester/wiki/Backport-Policy) for more details.

- UI Issue:  
    When an issue needs a UI changes, adding `require/ui` label creates an UI issue. It won't take any effect when issue contains a `area/ui` label.

- E2E Issue:  
    When an issues needs an E2E tests, adding `require/auto-e2e-test` label creates an E2E issue in harvester/tests repository.