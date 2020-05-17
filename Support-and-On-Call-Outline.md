## Responsibilities
When you are on-call, you will be responsible for the following:

### 1. Triaging & resolving issues from PagerDuty
Most incidents flow from Sentry into PagerDuty, though a few also come from our Systems Dashboard page. When an incident is raised, do your best to find the cause of the failure (e.g. Morningstar did not have data due to a holiday, etc.). As you gain experience you'll be expected to dig deeper into the root causes of these incidents.

### 2. Providing information & support to the Chicago team
The `engineering-support` Slack channel is where the Chicago team posts any issues or incidents. Your responsibility is to be the point person in the channel. If someone reports an issue, you are the one who responds with updates, resolutions, etc. This is one of the main responsibilities which is you should be the funnel in which all information is going through so there is confusion and things do not get dropped. 

### 3. Leading any remediation efforts when bugs are discovered
While you're on-call, you may be asked to work on small bugs that you and your teammates come across. This is one of the best ways to gain exposure to areas of the codebase that you don't normally work in.

### 4. Performing the weekly release
On Wednesday evenings at 10PM ET, you will perform the weekly production release. More details can be found on the [YCharts systems wiki page](https://github.com/ycharts/ycharts_systems/wiki/Deploy-and-Hotfix).

### 5. Leaving support better than you found it
Alert fatigue is something we take seriously. If we see things failing repeatedly then we need to either fix them so the next person doesn't have to deal with it, or not trigger an alert. There is no point in having alerts that people ignore. If we as a team each make this same effort while on support the small things don't pile up and become less of a burden. 

Of course, not all bugs are easily solvable, and all support incidents have varying impacts on our operations. However, these items should take priority over your normal day-to-day responsiblities for your on-call period.

## Expectations
* Please arrive at the office by 9:15am so you are ready to begin work at the "market open" time of 9:30am. This will allow us to respond quickly in the event of an incident. If you cannot be in the office before this time, let your team lead know so they can cover any incidents.
* Ensure you have the following Slack channels set to "Always Notify" while you are on-call:
    * `engineering-support`
    * `engineering-oncall` (NOTE: the PagerDuty bot will tag you in all messages, but this will keep you in the loop of any conversations in the channel)
* Make sure you have either the PagerDuty app on your phone, or have SMS alerting enabled in your PagerDuty account.
* Make sure you have access to our AWS CloudWatch console before you start your on-call period.
* Reply promptly to inquiries from the Chicago team, even if you don't have a resolution prepared let them know you are addressing it. It is always difficult to manage communication across remote teams, but keeping a tight feedback loop between NY and Chicago benefits all of us.
* Provides a support hand-off to the next engineer on support. The point of this is to let anyone who is coming on to support to be made aware of any ongoing issues or things to look out for. 

## Other Notes
* If you are scheduled to be on-call while you're on PTO, make sure to schedule coverage with one of your teammates!
* No one knows every dark corner of a codebase, especially one as large and complex as ours. Do not worry if you don't know the answer to a support question, or if you don't know how to resolve a PagerDuty incident. Be sure to ask as many questions as you can when you're starting out, and eventually you'll pick up the right patterns & methods for finding the answers.