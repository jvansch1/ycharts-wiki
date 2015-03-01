### YCharts Mechanical Turk

## Contents
- [Intro](#intro)
    - [Basic Definitions](#basic-definitions)
- [MTurkHITMixin](#mturkhitmixin)
    - [MTurkHITMixin Basics](#mturkhitmixin-basics)
    - [MTurkHITMixin Advanced](#mturkhitmixin-advanced)
        - [Attributes](#attributes)
        - [Flow](#flow)
        - [Methods](#methods)
- [MTurkHITAdminMixin](#mturkhitadminmixin)
    - [Implementation](#implementation)
    - [Advanced Implementation](#advanced-implementation)
- [Implementing Into Your App](#implementing-into-your-app)
    - [Create HIT](#create-hit)
    - [Add to LongRunningCommand](#add-to-longrunningcommand)

## Intro
There are several aspects surrounding Mechanical Turk that should be implemented when implementing Mechanical Turk
into an app. These include:

1. Implementing the `MTurkHITMixin` into a `MTurkFooHIT` model
    - This is the meat of the Mechanical Turk application, and is what handles all the interfacing with Amazon, as well
    as the basic structure for interacting with `Foo`
    - The `MTurkFooHIT` model essentially represents a HIT on Amazon
1. Implementing the `MTurkHITAdminMixin` into `MTurkFooHITAdmin`
    - This is not *required*, however, it is strongly recommended as it provides an interface for developers to interact
    with the HITs easily as required by our needs
1. Implementing `MTurkFooHIT` into `Foo`
    - This differs depending on `Foo` but should follow some basic structure.

Everything that is needed to implement Mechanical Turk is inside the `apps.main.utils.mturk_utils` module. In general, it
will likely be necessary to import only from `apps.main.utils.mturk_utils.mixins` and, depending on how Mechanical Turk is
implemented in an app, `apps.main.utils.mturk_utils.exceptions`.

### Basic Definitions
##### HIT
A *HIT* is any singular thing that is used to get data from Mechanical Turk. It will have multiple [Assignments](#assignment)
assigned to it and can be grouped into [HIT Groups](#hit-group). Generally, you will have one HIT per data item you wish to find.

##### Assignment
An *Assignment* is one response to a *HIT*. Each [Worker](#worker) can complete one assignment per [HIT](#hit) and assignments
are used to provide multiple answers to a question.

##### HIT Group
A *HIT Group* is a group of [HITs](#hit) which allows [Workers](#worker) to just hit a next HIT button and work on another,
similar HIT. HIT Groups are *not* user-defined, rather they are created by Amazon when multiple HITs have the same title.

##### Worker
A *Worker* is one of the "robots" that answers a question on Mechanical Turk.

##### Qualification
A *Qualification* is assigned to a [Worker](#worker) and can be used to determine who can complete a [HIT](#hit). We use
a basic qualification called **YCharts Quality Score** to keep track of a worker's performance and disallow workers that
are consistently bad. We use other various qualifications to keep track of other metrics and give bonuses/achievements/etc.

##### Assignment Dict
The *Assignment Dict* is our wrapper of the data we receive from Amazon about an [Assignment](#assignment). This is just
a normal Python `dict` which contains information about the assignment and [Worker](#worker) and is how the answers will
be accessed by us.

## MTurkHITMixin
### MTurkHITMixin Basics
This section contains the absolute basics to implement the mixin.

Your model should inherit from `apps.main.utils.mturk_utils.mixins.MTurkHITMixin` and must implement several methods
as documented below. For an in-depth look at how the mixin actually works and when each method is called, etc. see the next section.
For examples, see `apps.indicators.models.MTurkIndicatorImportHIT`.

```python
from apps.main.utils.mturk_utils import MTurkHITMixin

class MTurkFooHIT(MTurkHITMixin):
    """
    Model to get Bar from Mechanical Turk and add to Foo
    """
    # Add any additional fields if necessary
    foo = models.ForeignKey(Foo, related_name='mturk_hits')

    # Your model mus implement these methods
    def add_answers_to_assignment_dict(self, assignment_dict, assignment):
        """
        Takes an assignment_dict which already contains the basic info and
        the assignment object received from Amazon.

        This method should add the MTurkFooHIT specific answers to the assignment_dict.
        These are accessed from assignment.answers.
        """
        pass

    def create_hit_content(self, *args, **kwargs):
        """
        Define custom logic to create the content of the HIT. This includes the
        overview, questions, description, etc.

        It must return a dict with all of the keys below

        Any *args, **kwargs passed to create_hit will be passed through to this,
        and any attributes should be set in this as well such as self.agreements_required
        """
        hit_content = {
            'title': 'MyTitle',      # str; title of the HIT
            'description': '',       # str; the description shown in Amazon's HIT listings
            'reward': 0.03,          # float; price per HIT
            'duration': 900,         # int; time, in secs., a worker has to complete the HIT
            'approval_delay': 3600,  # int; time, in secs., until Amazon auto-approves submitted HITs
            'keywords': '',          # str; keywords for searching on Amazon
            'lifetime': 3600,        # int; time, in secs., until the HIT expires after creation
        }

        # Create the QuestionForm here, see MTurkIndicatorImportHIT
        question_form = boto.mturk.question.QuestionForm()
        hit_content['questions'] = question_form

        return hit_content

    def assignments_agree(self, assignment_dict_one, assignment_dict_two):
        """
        Returns True if two assignment_dicts agree, False if not

        This logic will be custom to whatever Foo does
        """
        pass

    def process_answer(self, answer, change_needs_review=True):
        """
        Receives an answer, in the form of an assignment_dict, containing the
        agreed upon answer and does whatever Foo needs to do with that answer,
        e.g., save an indicator
        """
        pass

    # Optional methods to override
    # Default return values shown
    def answer_is_valid(self, answer, num_agreements):
        """
        Receives an answer, in the form of an assignment_dict, containing the
        agreed upon asnwer and checks if it is valid. Useful to ensure that
        the agreed upon answer is the correct type, e.g., is not None, etc.
        """
        return True

    def create_feedback_message(self, assignment_dict, answer=None, reject=False):
        """
        Creates a custom feedback message when accept/rejecting an assignment.
        Takes the assignment_dict to give access to the answer received for the
        assignment and the accepted answer agreed upon by consensus.
        If the feedback is for rejecting an assignment, reject=True.
        """
        if reject:
            feedback = 'Your answers were incorrect.'
        else:
            feedback = 'Thank you!'
        return feedback

    @property
    def data_url(self):
        """
        Returns a string of the url to the data that the Worker is looking at.
        Used in the admin view assignments to help with checking answers manually.
        """
        return ''

    def __unicode__(self):
        """
        Standard Django thing, but useful to override so you know what you are looking at
        since a lot of logging relies on this
        """
        return '{0} {1}'.format(self.__class__.__name__, self.hit_id)

    def perform_additional_approval_tasks(self, ad):
        """
        Called after approving a HIT, for any additional tasks such as notifying workers
        """
        self.perform_additional_tasks(ad)

    def perform_additional_rejection_tasks(self, ad):
        """
        Called after rejecting a HIT, for any additional tasks such as notifying workers
        """
        self.perform_additional_tasks(ad)

    def perform_additional_tasks(self, ad):
        """
        Convenience method, for when you need to perform the same tasks after both approval
        and rejection, so that having two identical methods isn't necessary
        """
        pass

```
### MTurkHITMixin Advanced
#### Attributes
```python
hit_id = models.CharField(max_length=50)                # HIT ID
active = models.BooleanField(default=False)             # if HIT is active
assignments_required = models.IntegerField(default=3)   # number of total assignments
agreements_required = models.IntegerField(default=2)    # number of agreements for correct answer
num_new_assignments = models.IntegerField(default=0)    # number of new assignments since last reopen

last_reopen_time = models.DateTimeField(null=True, blank=True) # time HIT was last reopened
needs_review = models.BooleanField(default=False)       # if HIT needs human review
create_date = models.DateTimeField(auto_now_add=True)   # when HIT created
modify_date = models.DateTimeField(auto_now=True)       # when modified
last_updated = models.DateTimeField(null=True, blank=True)  # last time data saved
comments = models.TextField(blank=True, default='')     # comments from Workers
```
This section documents the flow through the mixin. Methods are shown here without the `self`
argument.

#### Flow
1. `create_hit(*args, **kwargs)` should be called after creating the `MTurkFooHIT` object.
These \*args, \*\*kwargs will be passed through to `create_hit_content(*args, **kwargs)` which
is where you should define your custom logic to set instance attributes.

1. `create_hit` then appends a confused and comments question onto the HIT and sets the required
qualifications, before making a call to Mechanical Turk to actually create the HIT.

1. `apps.main.management.commands.main_mturk_process_hits` is running and continuously calling
`handle_hit()` on all objects which have `active = True`

1. `handle_hit()` calls `is_processable()` which returns a tuple `(processable, new_assignments, old_assignments)`

    1. `is_processable()` checks to see if there are any new assignments since the last time the HIT was opened
    and checks if the number of new assignments is greater than the number of agreements required (`self.agreements_required`)

    1. If it is, then it returns `(True, new_assignments, old_assignments)` where `new_assignments` is a list of
    new assignments and `old_assignments` is a list of old assignments that haven't been approved or rejected, but
    will not be considered for finding a correct answer any more.

    1. Additionally, if new assignments are found, but not greater than the agreements required, and
    more than 2 days have passed since creating the HIT, it will be marked for review

1. If the HIT is processable, `handle_hit()` then calls `check_for_agreement(new_assignments, old_assignments)`
which returns a tuple `(agreement, answer, accept_assignments, reject_assignments, num_agreements)`

    1. `check_for_agreement` groups `new_assignments` into groups based on the agreement of their assignment_dicts
    and each group has a representative `assignment_dict` containing the agreed upon answer. This is done by called
    `assignments_agree(group['dict'], assignment_dict)` and adding the assignment to the group which returns `True`
    or creating a new group if all return `False`

    1. If a group is larger than the agreements required, it is considered the majority group. Any assignments in
    this majority group, as well as any `old_assignments` agreeing with this group are put into a list `accept_assignments`
    and any other assignments not agreeing are put into a list `reject_assignments`

    1. If no majority group is found, then it calls the `secondary_majority_check(groups)` which will return
    a majority group if one is found. This is for any conditions if the number of agreements is less than
    required but you still want to accept an answer.

    1. If no majority group is found, then it returns `(False, None, None, None, None)`

1. If `agreement is False` but there are still assignments available to be done, then `handlie_hit()` does nothing.
If all assignments have been done and there is no agreement, `handle_hit()` marks it for review, triggering an email to be sent.

1. If there is an agreement, then `answer_is_valid(answer, num_agreements)` is called to check the validity, marking it for review if
not valid.

1. If the answer is valid, then `process_answer` is called. Immediately after this, all assignments in `accept_assignments`
are approved and all assignments in `reject_assignments` are rejected.

#### Methods
###### Methods Required to Override
- `add_answers_to_assignment_dict`
- `create_hit_content`
- `process_answer`
- `assignments_agree`

###### Methods OK to Override
- `answer_is_valid`
- `create_feedback_message`
- `data_url`
- `__unicode__`

###### Methods OK to Override If You Know What You Are Doing
- `create_content`
    - For removing comments/confused and/or changing qualifications
- `create_assignment_dict`
    - For if comments/confused are different
- `approve_assignment`
    - If you need to change how assignments are approved
    - Default behavior is to add 1 to **YCharts Quality Score**, provide feedback, update achievements,
    and approve to Amazon
- `reject_assignment`
    - If you need to change how assignments are rejected
    - Default behavior is to subtract 9 from **YCharts Quality Score**, provide feedback, update achievements,
    and approve to Amazon
- `handle_achievements`
    - If you need to change how achievements work

###### Methods You Have No Business Overriding Unless You **REALLY** Know What You Are Doing
- `connection`
- `attempt_mturk_call`
- `get_all_assignments`, `get_new_assignments`, `get_unreviewed_assignments`
- `get_latest_assignment_date`
- `get_qualification_score`, `set_qualification_score`
- `reset_worker_qualifications`, `assign_worker_qualifications`
- `reopen_hit`, `expire_hit`, `unexpire_hit`, `delete_hit`, `create_hit_dict`

## MTurkHITAdminMixin
### Implementation
To implement the `MTurkHITAdminMixin`, create a `MTurkFooHITAdmin` class subclassing `MTurkHITAdminMixin`.

By default, the following attributes are set

```python
readonly_fields = ['active', 'create_date', 'modify_date', 'hit_id']

list_display = ('last_reopen_time', 'last_updated', 'create_date',
                'num_new_assignments', 'active', 'needs_review')

list_filter = ('active', 'needs_review', 'last_reopen_time',
               'last_updated', 'create_date', 'num_new_assignments')

actions = ['delete_hit', 'reopen_hit', 'expire_hit', 'unexpire_hit',
           'approve_all_assignments', 'reject_all_assignments',
           'flag_for_review', 'unflag_for_review', 'get_response_time_statistics']

fieldsets = (
    ('Basics', {
        'fields': ('hit_id', 'active', 'needs_review', 'comments'),
    }),
    ('HIT Utils', {
        'fields': ('assignments_required', 'agreements_required', 'num_new_assignments'),
    }),
    ('HIT Stats', {
        'fields': ('last_reopen_time', 'last_updated', 'create_date', 'modify_date'),
    }),
)
```

In order to change these, e.g., add fields to `list_display`, override the `__init__`

```python
class MTurkIndicatorImportHITAdmin(MTurkHITAdminMixin):
    def __init__(self, *args, **kwargs):
        self.list_display = ('indicator',) + self.list_display
        self.fieldsets = (('Indicator Specific', {
            'fields': ('indicator', 'date_format', 'quarterly_date_regex'),
        }),) + self.fieldssets
        super(MTurkIndicatorImportHITAdmin, self).__init__(*args, **kwargs)
```

You can, of course, add other fields and everything else like normal.

### Advanced Implementation
The above will give you a nice admin `change_list` and basic admin `change_form` but if you want to take
advantage of `apps.main.utils.mturk_utils.admin_views` for buttons on the `change_form`, you need to create
a template in the following location

```
/sites/ycharts/templates/admin/[myappname]/[mymodelname]/change_form.html
```

which looks like the following

```html
{% extends "admin/mturk/mturk_change_form.html" %}
```

The views included in `apps.main.utils.mturk_utils.admin_views` should just work with any model. You are also free
to define and add your own custom views to this, such as to manually input data. To do so, simply add the following

```html

{% block object-tools-items %}
<li><a class="btn primary" href="{% url 'admin_mturk_indicators_input_data' 'mturkindicatorimporthit' object_id %}">Manually Input Data</a></li>
{{ block.super }}
{% endblock %}
```

You must also create a template at

```
/sites/ycharts/templates/admin/[myappname]/mturk_view_assignments.html
```

This must contain the following

```html
{% extends 'admin/mturk/mturk_view_assignments.html' %}

{% block assignment_data_header %}
<!-- Put your specific data names here -->
<th>Date</th>
<th>Data Value</th>
{% endblock %}

{% block assignment_data %}
<!-- Put the corresponding assignment_dict values here -->
<td>{{ a.dict.date }}</td>
<td>{{ a.dict.value }}</td>
{% endblock %}
```

This will add your specific data to the table on the view assignments page

You can add a link to the foreign key (e.g., indicator or company) in the change list and change form in the
following way:

1. Adding a new method which returns the link
1. Adding this method to `self.readonly_fields` and `self.fieldsets` for change form
1. Adding this method to `self.list_display` for the change list

```python
class MTurkIndicatorImportHITAdmin(MTurkHITAdminMixin):
    def __init__(self, *args, **kwargs):
        self.readonly_fields = ['indicator_link'] + self.readonly_fields
        self.list_display = ('indicator', 'indicator_link',) + self.list_display
        self.fieldsets = (('Indicator Specific', {
            'fields': ('indicator', 'indicator_link', 'date_format', 'quarterly_date_regex'),
        }),) + self.fieldssets
        super(MTurkIndicatorImportHITAdmin, self).__init__(*args, **kwargs)

    def indicator_link(self, item):
        return format_html('<a href="{0}">Indicator Link</a>'.format(
            reverse('admin:indicators_indicator_change', args=(item.indicator.id,)),
        ))
    indicator_link.allow_tags = True
```

## Implementing Into Your App
### Create HIT
Once you decide when and where you need the HIT to be created, you should get all existing `MTurkFooHIT` objects
that will create the same HIT you are trying to create which are still active, and expire and deactivate them by
calling `delete_hit()`

Then it is as simple as creating the `MTurkFooHIT` object in the database (`MTurkFooHIT.objects.create(...)`)
and calling `create_hit(*args, **kwargs)`, passing in whatever `*args` and `**kwargs` your app needs.

### Add to LongRunningCommand
In `apps/main/management/commands/main_mturk_process_hits.py`, add your `MTurkFooHIT` object to `self.hits` in
`init_data()`
