This document is provided to give some lose guidelines around implementing a new type of security that is either user-created like a Model Portfolio or Custom Security or data provider-driven like a Separate Account or Mutual Fund.


### General App Guidelines:
1. **Frontend Integration:** Make sure the user can add the new security type to the app state on the frontend. This may require adding the new security type to an autocompleter or updating the frontend redux state.
1. **Backend - Single Security Integration:** If the application supports adding individual securities to the sate then ensure the new security type can handled properly by the backend. 
1. **Backend - Securitylist Member Integration:** If the application supports the user of securitylists then make sure the backend of the application can handle the new security type as a member of a securitylist.
1. **Export Integration:** If the application supports exporting data make sure the backend can export data with the new security type.
1. **Feature Permission Checking:** If the new security type requires special permissions make sure the frontend and the backend check the permission correctly. 
1. **Owner Permission Checking:** If the new security type is user created then make sure both the frontend and the backend check that the user has the correct access to view the security.
1. **Sharing:** If the new security type can be shared and the application supports sharing make sure that a user can't share the application state unless all the securities are also shared.
1. **Deletion Signals:** If the application allows users to save the state, like a saved Timeseries Table, then you must ensure that the proper Django Signals are in place so that when the new security type is deleted it is removed from the state. You must also ensure that the security is removed from the [User's default app state](https://github.com/ycharts/ycharts/blob/9790cbc98dc69bcd88b17ec695cfb318642a1f9d/apps/charts/signals.py#L107-L120) as well.
    - [Deletion Signal Example](https://github.com/ycharts/ycharts/blob/abd7eca0910c82e08c454949bcd55f443ef77927/apps/mutual_funds/apps.py#L36-L38)
1. **Autocompleter:** The new security type will need to be present in the autocompleter so an `AutocompleterModelProvider` will need to be created. 
    - [Autocompeleter Example](https://github.com/ycharts/ycharts/blob/6f45b5e9a3db9889d1a7937f44769fad991304eb/apps/mutual_funds/autocompleters.py)
1. **