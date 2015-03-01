### Vocabulary
* **Outsourcer**: A django model which manages one particular outsourced task. The importer creates one of these and tells it to create an outsourced task immediately afterwards.
* **Outsourced Task**: The actual task itself (ex. HIT for Amazon MTurk). Outsourcers are always linked to outsourced tasks by an ID.
* **Assignment**: A data strucure containing the actual responses that someone gave. Each assignment can hold one Worker's answers.

### Outsourcer States
* **Active**: An **active** outsourcer is one which has not yet done anything. All active outsourcers are associated with a valid outsourced task. Outsourcers are deactivated by the `indicators_outsourced_task_import` script whenever data is saved. They can also be deactivated through the Admin.
* **Not Active (Deactivated)**: A **deactivated** outsourced Task (`active == False`) is one which has served its purpose (ex. it imported data, it was deactivated manually for some reason, etc.). Deactivated Outsourcers cannot be reopened, expired, un-expired, etc. They are around only for historical purposes. That's it.
* **Needs human review**: An outsourcer is marked as needs_human_review if it encountered a problem of some sort which could not be resolved automatically (Ex. no consensus, weird date, etc.). The problem will be stated in the Outsourcer's *problem* field.

### Outsourcer Actions ###
* **Create Outsourced Task**: Creates an outsourced task to be handled by the Outsourcer that created it.
* **Delete Outsourced Task**: Deletes an outsourcer's outsourced task and deactivates the outsourcer.
* **Expire Outsourced Task**: Temporarily prevents further responses from being submitted for this Outsourcer. Think of it as a "pause" button. An expired Outsourcer can be un-expired. The most practical use case for this would be in testing. On production you would likely delete the Outsourced task instead.
* **Unexpire Outsourced Task**: Does the exact opposite of Expire Outsourced Task. Think of it as an "unpause" button.
* **Reopen Outsourced Task**: Generally, this allows additional assignments to be submitted for an Outsourced Task. The Amazon MTurk implementation adds `assignments_required` new assignments to the Outsourced Task, allowing `assignments_required` additional Workers to work on the task. When the task is reviewed by the `indicators_outsourced_task_import` script, only the new assignments will be considered when it checks for an agreement. Once an agreement is found, any of the old assignments which were not already approved or rejected will be compared to the new agreed upon answer. Those that agree will be approved and those that disagree will be rejected.

### Admin Actions ###
I will describe any functionality that is a) not obvious or b) has not already been explained above.

#### Main Admin Page ####
* **Get response time statistics**: Displays statistical information concerning the selected HITs, such as avg. response time, min/max response time, standard deviation. Good for gauguing how long people are taking to finish your Outsourced Tasks.
* **Flag for human review**: Marks the selected Outsourcers for human review.
* **Unflag for human review**: Unmarks the selected Outsourcers for human review.

#### Specific Outsourcer Page ####
* You can check/uncheck "Needs Human Review" as necessary. Outsourcers which need human review are not reviewed by the `indicators_outsourced_task_import` command, but workers may still submit assignments for them.
* **Last Updated**: The last time this Outsourcer saved any data.
* **Manually Input Data**: Allows you to manually enter a data point and save it on the indicator.

#### View Assignments Page ###
* **Approve Assignment**: Approves an assignment. For MTurk, this means paying the worker, increasing his scores and giving him any Achievements he may have earned. **Approving an assignment does not cause it to save any data automatically.**
* **Reject Assignment**: Rejects an assignment. For MTurk, this means decresing a worker's scores. For MTurk we still pay workers for rejected assignments, because not doing so = angry people + bad reputation. Instead, our Quality Score system is designed to weed out bad workers.
* **Save Assignment Data**: Save a given assignment's data point on the indicator.


## FAQ ##
### What can I do if there is no consensus? ###
* **Go to the admin and see what people said.** Then, look at the data table and decide who was right and who was wrong. Approve the correct answers, save one of them, and reject the incorrect ones.
* **Reopen the outsourced task.** This is the best option if you're feeling lazy. Reopening the task will create new assignments for additional Workers to complete. The `indicators_outsourced_task_import` script will only check assignments that were submitted *after* an outsourced task was reopened. If an agreement is found, any prior assignments that were not yet approved or rejected are evaluated alongside the correct answer. Those that agree with it are automatically approved. Those that do not are automatically rejected.
* **Enter the data yourself** and deactivate the outsourcer.

### Do I have to provide a reason when I approve or reject someone? ###
* Usually no. For the MTurk implementation, if you leave the feedback field blank, approved workers will receive a "Thank you!" and rejected workers will receive a message telling them that their answer did not agree with the consensus. If you are rejecting someone, I would highly recommend that you also choose to save one of the correct assignments at the same time. If you do this, the admin will know what the correct answer is and work it into the feedback message that the rejected worker receives. That way the worker will know what they did wrong.

### I messed up the instructions and want to redo the import. What should I do? ###
* Delete the Outsourcer so nobody else can respond, make your changes and then do a Forced Import.

### What happens to outstanding Outsourcers if I do a forced import? ###
* Any outstanding Outsourcers are deactivated, their outsourced tasks are deleted, and all of their unreviewed assignments are approved. You cannot have multiple Outsourced Taks open for the same indicator at the same time.

### What happens when I ban a worker? ###
* For MTurk, the Worker will simply have their YCharts Quality Score set to 0. This will prevent them from submitting additional assignments, and they will not be able to do your HITs again until the Qualification's `retry_delay` elapses. If you want to ban a worker forever, you can do a hard block through Amazon. Three of these from different requesters gets a worker's account suspended by Amazon, so only do this if a given worker seems to be a scammer of some sort.

### How do the MTurk Achievements work? ###
* The Achievements are built on Qualifications; As the worker completes our HITs, their scores change. Once a worker's score reaches a certain threshold, he receives an Achievement and a pay bonus. Each qualification has several score thresholds and Achievements associated with them. A file, `mturk_info.py`, contains all of the Achievement information. From this file you can change how much a worker receives in bonuses, how many achievements there are, what they are called, etc.
* Right now the system is designed to award Workers bonuses which increase in magnitude but also in spacing overtime. This increase is handled in such a way that the per-hit pay bonus is a constant, despite the seemingly increasing bonus payment amounts. To increase the magnitude of this effect, a multiplier is applied to the constant per-hit pay rate. The magnitude of this multiplier increases with each achievement rank but starts out below 1. Thus, we actually pay workers *below* the constant pay rate at first, and only the veterans get the actual constant rate. Once a worker moves beyond the maximum level for an Achievement, they are said to have "maxed" their qualification. Workers who have maxed their qualification receive a periodic bonus with a constant pay rate.

### What are the Qualifications? (MTurk Only) ###
Qualifications are named integers whose meaning we define that are associated with a given worker. They are handled entirely by Amazon. You can find more information about them in `mturk_qualifications.py`. If you create a new qualification, you will have to add its Qualification Type ID to `vendors.py`.

Qualifications:
* **YCharts Quality Score**: A number representing the quality of a worker's work. It starts out at 36, increases by 1 for each approved assignment and decreases by 9 for each rejected one. Once it dips below 0 (this threshold is specified in the internal_data and is 0 by default), a worker can no longer work on our HITs and must requres the qualification again after a delay period.
* **YCharts Approval Streak**: The number of approved HITs that the worker has in a row. There is an Achievement track for this.
* **YCharts HITs Approved**: The total number of HITs that this worker has had approved.
* **YCharts Early Bird**: The number of HITs that this worker has submitted w/in the first 12 minutes of the HIT being posted.
* **YCharts Total HITs**: The total number of HITs that this worker has completed.

## Amazon Mechanical Turk Gotchas ##

##### Delete Outsourced Task #####
HITs aren't actually deleted when you call Delete Outsourced Task. They are simply expired. We do this for the purpose of keeping historical records for settling any disputes that a worker may raise. The Outsourcer itself is deactivated though, which means it cannot be unpaused and will not be reviewed anymore. As far as *we* are concerned, it's gone.

