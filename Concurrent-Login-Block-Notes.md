# Blocking Concurrent Logins

We use a Middleware called `UserRestrictMiddleware` to block multiple users from logging in
to the same account. The flow is somewhat complicated:

On every request, the session is checked to see if that user has been marked as logged in elsewhere.

- **Logged In Elsewhere**:
    - The session is checked for the existence of the auth user keys
        - **Auth Keys Exist** means that the user is logged in so this is the first request after
        logging in on another computer
            - We mark this session as `force_logout` and move the `_auth_user_id` to `user_id`
            - We delete the auth keys, "logging out" the user
    - The request is checked for if it is an AJAX request
        - **Request is AJAX** means that we do not want to redirect, so we just pass through
        to the view. AJAX views will now all have a decorator marking them as requiring login
        to handle the user being logged out at this point.
        - **Request is NOT AJAX** means we will want to redirect
    - If we are redirecting, we delete the `logged_in_elsewhere` key so that we don't get stuck
    in a redirect loop when this middleware is called on the redirected page
    - Redirect to the user restrict log out page

- **Not Logged In Elsewhere**
    - The current session key is attempted to get from the cache for this user
    - If the session key is found, we check if it matches the current session
        - **Does NOT Match Current Session** means that this user is logged in elsewhere
            - We get the session which matches the previously stored session
            - We mark the old session as `logged_in_elsewhere`
            - We store the current session key in the cache
        - **Matches Current Session** means that this is still the current session so
        don't do anything
    - If the session key is not found, we store the current session key in the cache

## AJAX Logged In Elsewhere

When a user has logged in elsewhere, we want them to be logged out immediately. Relevant AJAX
end points now should be wrapped in a decorator `@login_required_ajax` which checks if the user
is logged in and if not, returns an error message which depends on if we forced them to logout
or not.

The different apps handle displaying these errors differently, but in any case, the functionality is removed
from the user.

## Non-AJAX Logged In Elsewhere

When a user has logged in elsewhere, the next page the load that is not AJAX will log them out
if not done so already from an AJAX call, and then it will redirect to a special user restrict log out
page with a message depending on whether the user was pro or not. The view which renders this page,
also creates a logging object with information and when and who this happened to. The view then also
deletes the session, ensuring the page is shown only once.
