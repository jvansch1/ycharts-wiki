## The Store

## Overview

The simple overview of steps for creating a subscription follow:

1. Create a User object (it's assumed that people can do this)
2. Get billing information for user into Braintree and link to our User
3. Create Subscription

Note: Reading .../docs/payments/braintree_admin_setup.txt is also worthwhile to understand how Braintree subscriptions work.

## Creating and Modifying Billing Information

Billing information is updated via [Transparent Redirects](https://www.braintreepayments.com/docs/python/credit_cards/create_tr). These are calls to Braintree directly from a form that passes encrypted information. They reduce our risk since we never have to store credit card data on our site, nor do we need to process it on our server.

In fact, we just submit credit card data to Braintree then store a token to access that payment info if the user wants to buy something.

The simplest versions of a Transparent Redirect in action can be viewed in:

    apps.store.views.account_billing() # Contains the view that displays the form
    apps.store.forms.EditBillingForm # Is the form with the TransparentRedirect call
    apps.store.braintree_utils.update_paying_entity() # Is the util function that creates
    # PaymentInfo objects (required for subscription creation) after successfully
    # creating a braintree payment method.

## Creating and Modifying Subscriptions:

Subscriptions in Braintree follow this [process flow diagram](https://www.braintreepayments.com/docs/python/subscriptions/overview).

This is the whole life of a subscription in code form:

    from django.contrib.auth import get_user_model

    from apps.store.models import Subscription, Tier

    # Get necessary data to create the subscription
    user = get_user_model().objects.get(email='user@example.com') # Get a user
    tier = Tier.objects.get(internal_name='pro_gold') # Get the Gold (Lite) Tier

    # Create the subscription
    subscription = Subscription.create_subscription(
        user=user,
        tier=tier,
        billing_plan=tier.billing_plan,
        discount_code=None, # This is a default value
        affiliate=None, # This is a default value
        created_via_feature=None, # This is a default value
        send_welcome_email=True, # This is a default value
        free_trial_duration=7 # This is a default value set with store.models.DEFAULT_TRIAL_DURATION
    )


    # To extend the trial within the trial period
    subscription.extend_trial(days=15) # Adds 15 days to the current trial and updates braintree plans accordingly


    # To cancel the trial early and bill immediately
    subscription.end_trial_and_bill()


    # To cancel a trial during or after trial.

    # This is the default - if converted, sets to pending cancel
    # Does not refund any previous billings.
    subscription.cancel(refund=None)

    # Prorated refund upon cancellation (and cancels immediately)
    subscription.cancel(refund='prorate')

    # Refund last payment upon cancellation (and cancels immediately)
    subscription.cancel(refund='last')

    # Refund all payments upon cancellation (and cancels immediately)
    subscription.cancel(refund='full')


    # Should you wish to reactivate a canceled (or pending cancel subscription)
    subscription.reactivate(bill_immediately=True)

## Interacting with Braintree

Don't do this in any files except for apps/store/utils/braintree.utils.py and store test files.

    # This is bad
    import braintree
    from braintree import <something>

    # This is good, as the module should have access to all endpoints you need.
    from apps.store.utils import braintree_utils

    # One exception - if you really need it, you can grab the NotFoundError
    # like in apps/store/admin_views.py, but that will be a rarity.
    # The import looks like this:
    from braintree.exceptions.not_found_error import NotFoundError

