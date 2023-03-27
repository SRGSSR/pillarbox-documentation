# GitHub Projects Setup

This article describes how we manage [our product](https://github.com/orgs/SRGSSR/projects/9) with [GitHub Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects).

## Settings

We currently use 3 settings:

- **Status** for the various statuses an issue might have in our workflow. Statuses must be defined in the following order so that board columns are ordered correctly:
  - **✏️ Draft**: Tasks which have not been sufficiently refined yet.
  - **📋 Backlog**: Tasks which are ready to be planned.
  - **🚧  In Progress**: Tasks currently being worked on.
  - **🔍 Testing**: Tasks currently being tested.
  - **✅ Done**: Completed tasks.
- **Sprint**: Duration of 1 week.
- **Platform**:
  - **✨ All**
  - **🤖 Android**
  - **🍎 Apple**
  - **🌍 Web**

## Workflows

Workflow management is currently limited on GitHub Projects. Here are the ones we kept and how they are set:

- **Item added to project**: When an issue or pull request is added set its status to _Draft_.
- **Item reopened**: When an issue or pull request is reopened set its status to _Backlog_.
- **Item closed**: When an issue or pull request is closed set its status to _Done_.
- **Pull request merged**: When a pull request is merged set its status to _Done_.

## Views

We have created the following views:

### Sprint board

Displays a board of the the current sprint:

- Layout: Board
- Fields: Title, Assignees, Status, Platform, Linked pull requests
- Column field: Status
- Field sum: Count
- Sort: Manual
- Filter: `-status:"✏️ Draft"`

### Drafts

Displays all issues to be refined per platform:

- Layout: Table
- Fields: Title, Platform, Labels
- Group: Platform
- Field sum: Count
- Sort: Manual
- Filter: `status:"✏️ Draft"`

### Testing

Display issues to be tested per platform:

- Layout: Table
- Fields: Title, Linked pull requests
- Group: Platform
- Field sum: Count
- Sort: Manual
- Filter: `status:"🔍 Testing"`

### Product Backlog

Displays the product backlog for all platforms:

- Layout: Table
- Fields: Title, Status, Milestone, Platform, Sprint, Labels
- Group: Status
- Field sum: Count
- Sort: Status-desc
- Filter: `status:"✏️ Draft","📋 Backlog"`

### Current Milestone

Displays the current milestone which represents the current focus:

- Layout: Table
- Fields: Title, Status, Platform, Sprint, Labels, Milestone
- Group: Platform
- Field sum: Count
- Sort: Manual
- Filter: `-status:"✅ Done","✏️ Draft" milestone:<name>`

### Sprint List

Displays issues per sprint:

- Layout: Table
- Fields: Title, Sprint, Platform, Status, Linked pull requests, Labels
- Group: Sprint
- Field sum: Count
- Sort: Sprint-desc
- Filter: `-no:sprint`

