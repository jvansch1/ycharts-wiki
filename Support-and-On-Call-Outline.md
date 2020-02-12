# Support & On-Call Outline

## Responsibilities
When you are on-call, you will be responsible for the following:
### Triaging & resolving issues from PagerDuty
Most incidents flow from Sentry into PagerDuty, though a few also come from our Systems Dashboard page. When an incident is raised, do your best to find the cause of the failure (e.g. Morningstar did not have data due to a holiday, etc.). As you gain experience you'll be expected to dig deeper into the root causes of these incidents.

### Providing information & support to the Chicago team
The `engineering-support` Slack channel is where the Chicago team posts any issues or incidents. Most inquiries deal with user-facing functionality, but may touch on areas of the codebase such as the Celery queues.

### Leading any remediation efforts when bugs are discovered
While you're on-call, you may be asked to work on small bugs that you and your teammates come across. This is one of the best ways to gain exposure to areas of the codebase that you don't normally work in.

### Performing the weekly release
On Wednesday evenings, you will perform the weekly production release. More details can be found on the [YCharts systems wiki page](https://github.com/ycharts/ycharts_systems/wiki/Deploy-and-Hotfix-%5BYCharts%5D).

Of course, not all bugs are easily solvable, and all support incidents have varying impacts on our operations. However, these items should take priority over your normal day-to-day responsiblities for your on-call period.

## Expectations
* Please arrive at the office by 9:15am so you are ready to begin work at the "market open" time of 9:30am. This will allow us to respond quickly in the event of an incident. If you cannot be in the office before this time, let your team lead know so they can cover any incidents.
* Ensure you have the following Slack channels set to "Always Notify" while you are on-call:
    * `engineering-support`
    * `engineering-oncall` (NOTE: the PagerDuty bot will tag you in all messages, but this will keep you in the loop of any conversations in the channel)
* Make sure you have either the PagerDuty app on your phone, or have SMS alerting enabled in your PagerDuty account.
* Make sure you have access to our AWS CloudWatch console before you start your on-call period.
* Reply promptly to inquiries from the Chicago team, even if you don't have a resolution prepared. It is always difficult to manage communication across remote teams, but keeping a tight feedback loop between NY and Chicago benefits all of us.

## Other Notes
* If you are scheduled to be on-call while you're on PTO, make sure to schedule coverage with one of your teammates!
* No one knows every dark corner of a codebase, especially one as large and complex as ours. Do not worry if you don't know the answer to a support question, or if you don't know how to resolve a PagerDuty incident. Be sure to ask as many questions as you can when you're starting out, and eventually you'll pick up the right patterns & methods for finding the answers.