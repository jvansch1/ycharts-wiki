## Overview

This wiki will outline what steps to take when you receive a PagerDuty incident for Airflow. 

Please note that any of the below steps you take to modify a DAG should be tested on staging first (https://airflow.staging.ycharts.com) before touching any production DAGs!

For more details on Airflow please check out our general Airflow wiki: https://github.com/ycharts/ycharts_systems/wiki/Airflow and also our documentation: `/sites/ycharts/ycharts/airflow/docs/airflow.md`

## PagerDuty Incidents

### task(s) missed an SLA in DAG=<dag_name>

This incident tells us that certain tasks missed an SLA, which means they finished later than expected. When you see this, follow the below steps:
- Click on the airflow url in the PagerDuty incident which will take you to a page that lists all the tasks that missed an SLA in that DAG.
- Click on the DAG under the `Dag Id` column (this should take you to the Graph View).
- Check all the tasks from the incident that missed an SLA and confirm they all succeeded (should have a dark green border around the task).
- Also check how long this task took to complete and the time in which it finished. You can check how long a task typically takes by clicking on a task in the Graph View, then going to `Task Instances`, and then sort the `Duration` column.
    - In most cases, a task just took slightly longer than it normally does, in which case we are fine and you can resolve the incident.
    - In other cases, a task started slightly later than normal, which typically happens overnight on Wednesdays due to production releases, and this is also fine to resolve.
    - However, if a task took way longer than it normally does or it did not succeed, this could be a potential issue and should be investigated further. Potential issues could be a task is hanging or stuck, you should check the logs for this command.

### CRITICAL: The <dag_name> has not been triggered on time.

This is a severe issue, as it indicates that one of our DAGs has not started running when it was supposed to. Since these DAGs control our data pipeline, this could cause our data importing / processing to be delayed and lead to missing data for the next day on our site.

To address this follow the below steps (will be doing this on staging first and then production):
- Go to the airflow staging web UI (https://airflow.staging.ycharts.com), click into the DAG that has the issue, and go to the `Graph View`.
- Check which date this DAG was triggered for by looking at the box next to `Run:`. If this date is before yesterday's date (this is important!), that means this DAG is holding us up so we need to check which task in this DAG is stalling us -- the tasks in light green are the ones still running and causing the issue, please note these down.
- We need to resolve this by succeeding the entire DAG.
    - Go to https://airflow.staging.ycharts.com and click on the Dag Run that is in a light green circle for the DAG with the issue like the image below shows. ![image](https://user-images.githubusercontent.com/16657367/98730535-6b61a900-236a-11eb-9902-375e80b4c803.png)
    - Click the checkbox on the left side and then under `With Selected` click `Set state to 'success'`.
- Now that this older Dag Run has been succeeded, the DAG for today should be triggered (this might take a couple minutes). Confirm this by clicking on the DAG and going to the graph view and check that a fresh DAG has been triggered and is running.
- Repeat the above steps for our production airflow UI: https://airflow.ycharts.com/