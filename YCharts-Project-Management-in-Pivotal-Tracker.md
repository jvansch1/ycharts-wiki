# Overview
YCharts uses [Pivotal Tracker](https://www.pivotaltracker.com/) to manage engineering projects at their most granular level. It is the tool we lean on to define distinct bodies of work that will be written, reviewed and merged into the codebase. It is important that we as a team use the tool effectively and consistently as we use it as a source of asynchronous communication between developers and engineering leaders. We should, for example, be able get a rough idea of how far along a project is by looking at how how many points of a large "epic" are completed. We should be able to tell what engineer is working on what exact task at any given time by looking at the "Current" column of a project board. And so on. 

# Project Boards

## Boards Basics
For YCharts, Project Boards represent the the collective work and output of a team. 

Currently, YCharts has 5 Project Boards

- **Data** - All data importing, calculating related projects
- **Applications** - All front-end application projects
- **Core** - PDF reports, quote pages and other projects that have a mix of front-end and back end components. Note, YCharts may have multiple Core teams but will share one Core project board
- **Systems** - AWS and developer infrastructure related projects
- **Excel** - Excel Add-in related projects

## Current/Backlog/Icebox
The Current, Backlog and Icebox columns of a project board should contain tickets that are in specific states. Care should be taken by each engineer to ensure their tickets are in the right column.

**Current** - Represents works that the engineer is currently working on, or has finished working on and is awaiting review. *Nothing* else should be in current. Looking at the Current column anyone should be able to see what any member of a team is working on.

**Backlog** - Represents work that is scheduled to happen next. They should be roughly ordered by the order in which they will be done. Tickets should only be in backlog if they are 100% going to get done at some point. Looking at the Backlog column anyone should be able to tell roughly how many weeks of work a team currently has scheduled. In other words, if a teams' backlog is 5 weeks long, we should know that they will not be able to take on any additional projects for 5 weeks. The backlog should be groomed at least bi-weekly by engineering leaders to ensure it accurately reflects what the team is scheduled to work on.

**Icebox** - Represents vague work or ideas that may turn into real/scheduled work at some point. An engineer may create a ticket in the icebox based on an idea they had while doing other work. Engineering leaders may create a ticket or tickets in icebox for work they'd like to see done that is not critical to any project being successfully delivered. Icebox is generally a place to stick work / ideas that we don't want to forget, but that may never actually get done.

## Epics
All medium to large sized projects (> 3 tickets) should be tracked on Pivotal Tracker using an Epic. Engineering leaders speccing the project should
1. Create an appropriately named epic and add a short description
2. Link to the project spec doc either in Github or Google Docs

The engineer leading the project should create tickets *with points* before starting the project, such that rough progress of the project can be estimated by looking at the percentage of delivered points.

# Ticket Management
The state and size of tickets should be appropriately managed such that for any epic we can:
- See the amount of work done/todo 
- See the exact status of any of the pieces

## Estimation
We use a 0-1-2-3 point estimation system for tickets.

- 0 point tickets are super-quick changes that might take 5 minutes or less An example might be adding a redirect to our nginx redirect file. 
- 1 point should be 2 hours or less of work
- 2 points should be roughly 4 hours of work
- 3 points should be 6 hours (for a whole day) of work

Further, bug tickets are always 0 points, as they are considered "the cost of doing business" and are thus not considered to be a part of team output.

If a ticket will take longer than a day to complete it should be split in multiple pieces if at all possible

## States
Tickets should be kept in the correct state as work progress.
- **Unstarted** - No work has started
- **Started** - Work has started
- **Finished** - Work has finished and is ready for review.
- **Delivered** - Work has finished the review process and has been merged into the parent branch, (e.g. `ycharts/develop`, a feature branch on `origin`, etc.). This means that tickets may be set to "Delivered" even if they are not merged into our main development branch/deployed to the staging environment/etc.
- **Accepted** - Work has been reviewed by QA team and was confirmed to function as necessary (NOTE: This state is currently irrelevant as no QA team exists, instead tickets that have been delivered will be accepted during a release by the release engineer)
- **Rejected** Work has been review by QA team and issues were found (NOTE: This state is currently irrelevant as no QA team exists)
