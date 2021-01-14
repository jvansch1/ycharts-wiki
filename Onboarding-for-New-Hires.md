# 1st Month
### Week 1
Your first week will be a little bit slower than most at YCharts as you get setup and meet our team. We encourage your to **write down as much as you can** and there are never too many questions.

- [ ] On your couple of days you will need to setup a few things. The following setup items are **required** not optional so do each of them:
    * Environment, IDE (We as a team use PyCharm and will make your life better if you use it)
      * [Download the latest Professional version](https://www.jetbrains.com/pycharm/download/#section=mac). Sign up for the trial and once it expires let your manager know so they can add you to the account.
    * Setup Wifi (`ycharts-secure`). You should have been given credentials to connect from your manager.
    * Setup VPN. You should have been given your VPN credentials to connect from your manager. [Instructions](https://github.com/ycharts/ycharts/wiki/YCharts-VPN-Setup-Instructions)
    * Setup YCharts Staging & Production account. 
        * Your account will not have a password so you will need to reset it in both environments. Your email is your ycharts email (i.e. xyz@ycharts.com)
            * Staging Password Reset Link: https://staging.ycharts.com/accounts/password_reset
            * Production Password Reset Link: https://ycharts.com/accounts/password_reset
        * Login to [YCharts Admin Site](https://ycharts.com/admin/) and setup your 2FA with your google authenticator.
    * Setup your Google calendar
    * make sure you accept the events you are invited to. We rely heavily on our google calendars for scheduling everything from daily meetings to 1 on 1s with your manager to reviews. Highly recommended to use the [slack plugin for google calendar](https://slack.com/app-pages/google-calendar). 
    * External Accounts Setup
      * Go through the [full list](https://github.com/ycharts/ycharts/wiki/Setting-Up-Accounts-for-New-Hires) accounts one by one and make sure you have access to all of them. You should have received emails from each of them asking you to confirm joining. If you don't have access and can not find the email for any of those accounts, please reach out to your manager. It is very important that you are able to login to each of them.
      * Download the slack desktop application [link](https://slack.com/downloads/mac)
        * Be sure to install 2 necessary plugins: [Google Calendar](https://ycharts.slack.com/apps/ADZ494LHY-google-calendar) and [Github](https://ycharts.slack.com/apps/A8GBNUWU8-github)
        * Once you have the Github Slack app installed follow these [instructions](https://help.github.com/en/github/setting-up-and-managing-your-github-user-account/managing-your-scheduled-reminders) to setup reminders so you know when PRs are approved, require changes, re-assigned, etc.
      * You need to add a gmail filter that archives away developer emails that are sent when you or another developer does something locally that causes an email to get sent. 
        * The screenshot of the filter you need to add is below, if you have problems adding it ask a colleague![filter](https://i.imgur.com/mAhxXge.png)
        * The XML Gmail filter is [here](https://gist.githubusercontent.com/KFoxder/8552361f1a59ba069db05fac2f801669/raw/d5996b599e5130f7dcae2bbd15c8b6e595e2e2b2/ycharts_engineering_gmail_filter.xml). Instructions on how to import a Gmail filter are [here](https://support.google.com/mail/answer/6579?hl=en)
    * Add Zenefits vacation calendar in Google Calendar so you know when other people are away.
      * [Instructions on how to add the calendar](https://help.zenefits.com/Time_Off/FAQs_About_Calendars_in_Zenefits/How_do_I_add_a_Zenefits_calendar_to_Google_Calendar%3F/)
      * Link to use in the above instructions: `webcal://secure.zenefits.com/custom_api/pto/cal/company_pto/591980?token=4wyCR_VQ_6&loc=ODc3NDY=&dept=`

    * Payroll/Insurance through Zenefits
    * 401k through ADP
- [ ] Get some sweet YCharts gear 👕 📓 ! 
- [ ] Download Slack and Pagerduty for your phone. You will need both in general, but definitely on support.
- [ ] Participate in our daily scrum.
- [ ] Setup time for you to meet with some of our leaders in Chicago as well as New York.
- [ ] Meet with your manager to go over what your goals are and setup a weekly meeting with them.
- [ ] Get a walkthrough of our site with the Product Manager and see what exactly we are selling.
- [ ] Start on some of the [required reading list](https://github.com/ycharts/ycharts/wiki/Required-Reading-List).
- [ ] Do the [git tutorial](https://github.com/ycharts/ycharts/wiki/Onboarding-for-New-Hires#git-tutorial).
- [ ] Do the [Django tutorial](https://github.com/ycharts/ycharts/wiki/Onboarding-for-New-Hires#django-tutorial) if it applies to your role.
- [ ] Review with an engineer your Django Polls App from the Django Tutorial
- [ ] Setup your [YCharts development environment](https://github.com/ycharts/ycharts/wiki/Developer-Environment-Setup). (This will usually take an entire day).
- [ ] Meet with your manager and you will pick up your first pivotal ticket to work on.
- [ ] Submit your first Pull Request by the end of the week 1.
- [ ] Everyone is expected to show up before 9:45 AM and before 9:30 AM (Market Open) if you are on support.

### Week 2-3
After getting your feet wet in the first week, you will get into our codebase and do some small tickets and finish your Aptible Information Security training.
- [ ] Finish Aptible Information Security training that should have been assigned to you.
- [ ] Continue working through the [required reading list](https://github.com/ycharts/ycharts/wiki/Required-Reading-List).
- [ ] Pick up a couple of small tickets.
- [ ] Ask questions


### Week 4
You should start to feel a little more at home in your environment setup as well as our codebase.

- [ ] Meet with your manager to go over your first PR and ticket that you submitted in week 2/3.
- [ ] Start reading a couple books from our list of educational resources.
- [ ] Finish and submit another pull request by end of the week 4.

# 2nd Month
As you get more and more comfortable with our codebase and our development process you will take on more responsibility as the months progress.

- [ ] Fly co-pilot on a weekly release [Deployment Process](https://github.com/ycharts/ycharts_systems/wiki/Deploy-and-Hotfix)
- [ ] Perform a weekly release
- [ ] Be on-call for one day a week (check out [the outline](https://github.com/ycharts/ycharts/wiki/Support-and-On-Call-Outline) for more details!)

# 3rd Month

- [ ] Perform a weekly release
- [ ] Be on-call for a week

***

# Git Tutorial
Whether you know git or not this is a great [Git tutorial](http://learngitbranching.js.org/) to go through.


# Django Tutorial
Create a Django Polls app by following the [Django Tutorial](https://docs.djangoproject.com/en/2.2/intro/tutorial01/).
Deliverable should be a working Django Polls app on your github repo called `django-polls`. 

> As you go through creating it, ask Engineers any questions you have or if you need any help!



# Walkthroughs
After reading the above resources and having a look at the codebase any new hire should have multiple walkthroughs with a Product Manager as well as Engineers. These should be scheduled during the first week. If not speak up please!

| Type  | Description | 
| ------------- | ------------- |
| Product | Product Manager should walk through all the different parts of the site, what they do and why they are valuable for our customers. |
| Frontend | Engineer should go over how Django and Angular play nice. How CSS and JS work and what Django Templates are. | 
| Backend | Engineer should go over our Django codebase. | 
| Systems Architecture | Engineer should explain what YCharts looks like from a systems perspective. What are Admin vs. Web servers, how does the queue work? | 
| Development Process | Engineer should go over how we release code from getting a requirement in Pivotal Tracker to releasing it to the development branch. Also should cover our use of git. | 