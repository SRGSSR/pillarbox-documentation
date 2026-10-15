# GitHub Projects Setup

This article describes how we manage [our product](https://github.com/orgs/SRGSSR/projects/9) with [GitHub Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects).

## Settings

We currently use 3 settings:

- **Status** for the various statuses an issue might have in our workflow. Statuses must be defined in the following order so that board columns are ordered correctly:
  - **📋 Backlog**: Tasks which are ready to be planned.
  - **👀 Follow**: Tasks that are followed across sprints.
  - **🚧 In Progress**: Tasks currently being worked on.
  - **🍿 Code Review**: Tasks currently being reviewed.
  - **✅ Done**: Completed tasks.
- **Sprint**: Duration of 1 week.
- **Platform**:
  - **✨ All**
  - **⚙️ Backend**
  - **🤖 Android**
  - **🍎 Apple**
  - **🌍 Web**

## Workflows

Workflow management is currently limited on GitHub Projects. Here are the ones we kept and how they are set:

- **Item added to project**: When an issue or pull request is added set its status to _Backlog_.
- **Item reopened**: When an issue or pull request is reopened set its status to _Backlog_.
- **Item closed**: When an issue or pull request is closed set its status to _Done_.
- **Pull request merged**: When a pull request is merged set its status to _Done_.
- **Auto-add to project** (one rule per repository): When an issue or PR is opened for a repository, add the item to the project.

## Views

We have created the following views:

### Sprint board

Displays a board of the the current sprint:

- Layout: Board
- Fields: Title, Assignees, Platform, Linked pull requests, Milestone, Type
- Column field: Status
- Field sum: Count
- Sort: Manual
- Filter: `sprint:@current -status:"👀 Follow"`

### Triage

Displays issues that require triage:

- Layout: Table
- Fields: Title
- Group: Platform
- Field sum: Count
- Sort: Manual
- Filter: `label:triage status:"📋 Backlog"`

### Follow

Displays issues that require triage:

- Layout: Table
- Fields: Title, Assignee, Status, Platform, Type
- Group: Platform
- Field sum: Count
- Sort: Manual
- Filter: `status:"👀 Follow"`

### Product Backlog

Displays the product backlog for all platforms:

- Layout: Table
- Fields: Title, Status, Milestone, Platform, Sprint, Labels, Type
- Group: Platform
- Field sum: Count
- Sort: Status-desc
- Filter: `-status:"✅ Done" -sprint:@current`

### Sprint List

Displays issues per sprint:

- Layout: Table
- Fields: Title, Sprint, Milestone, Platform, Status, Linked pull requests, Labels, Type
- Group: Sprint
- Field sum: Count
- Sort: Sprint-desc
- Filter: `-no:sprint`
