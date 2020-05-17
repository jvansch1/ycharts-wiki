### Overview

[Git Secret](https://git-secret.io/) is the library we use to encrypt all our sensitive data that is checked in our repository. It allows us to encrypt entire files with private keys for every person that needs access. That way we each have a way to decrypt it at a individual level and can revoke permission at an individual level. 

### Why do we use it?
When developing software there are many scenarios where the code you are writing requires the use of sensitive data. That data could be a username & password to login to an FTP server or an API key to access an API. Storing this type of data in plain text in git repository is never a good idea since it would allow anyone to easily access it. 

We need a way to check-in this data to our repository so it is version controlled but also a way to not give access to unwanted parties.

### Important Notes;

- If you touch any part of a file the plaintext version of a file that is an encrypted file. For example `ycharts/settings/secrets.py`. You need to re-encrypt the file by running `git secret hide -m` and commit the changes.


### Useful commands

`git secret list` - See all the files that are encrypted in the code.

`git secret whoknows` - See all the people that have permission to decrypt files.

`git secret reveal -f` - Decrypt all the encrypted files and overwrite any plaintext decrypted files already present.

`git secret hide -m` - Encrypt any plaintext files into `.secret` files if they have been modified. 

### Example of how it works
We will go over a quick example of how git secret works. 

**Context:**

We are writing a very simple API to pull the weather data in from weather.com. We get an API key (XYZ123) that we use to access weather.com's API. 

1. We need to add this API key to the git repo so we create a file called "api_key.py"
    ```py
    # api_key.py
    WEATHER_API_KEY = "XYZ123"
    ```
2. We can't check this file into the repo like this or everyone will that ever has access to the repo will be able to see it. So we run ```git secret add <path>/api_key.py``` which does two things:
    1. It adds `api_key.py` to our `.gitignore` file so git knows to never check in this file.
    2. It adds `api_key.py` to our `.gitscret/paths/mapping.cfg` which tells git secret that this file should be decrypted
3. We now need to encrypt the file so we run `git secret hide -m`. This now creates an encrypted version of `api_key.py` named `api_key.py.secret`. This is the encrypted version of the file so we no longer need `api_key.py` to be checked in. If we need `api_key.py` we can retrieve it by running: `git secret reveal -f`
4. We now commit the following files: `api_key.py.secret`, `.gitsecret/*` to our codebase so everyone who has access can pull down our changes and decrypt the files.

We now have a file `api_key.py.secret` that is on our repo and holds the weather.com API key but is only viewable to us.

If you want a deeper dive, you can checkout [git-secret.io](https://git-secret.io/) or ask any more senior engineer on the team to give you a deeper explanation. 


### Frequently Asked Questions

**How do you setup Git Secret for someone new?**

Follow the steps [Setting up Git-Secret](https://github.com/ycharts/ycharts/wiki/Developer-Environment-Setup#setting-up-git-secret) that is part of the developer environment setup.

**How do remove someone from git secret so they no longer can access the encrypted files?**

To remove someone from git secret so they can no longer decrypt the `.secret` files you need to:
1. Run `git secret whoknows`. This will give you a list of all the people. Identify the person you want to remove based on their email. 
1. Run `git secret killperson <email>`. This will now remove the person's key from the key ring located `.gitsecret/keys/pubring.kbx` 
1. Run `git secret hide`. This will re-encrypt all the files that are encrypted but it will encrypt it with everyone's key _except_ the person whos key was removed from `.gitsecret/keys/pubring.kbx`.
1. Now commit the changes to all the files 

**How do you encrypt a new file that has sensitive data?**

1. Add the file by running `git secret add <path>/<filename>`. This will add it to the `.gitignore` and `.gitsecret/paths/mappings.cfg`.
1. Encrypt the file by running `git secret hide -m`. This will create the encrypted file `<filename>.secret`. 
1. Commit the changes to the repo.

**How do you remove a file that is encrypted?**

1. Run `git secret remove <path>/<filename>`.

**How come the git pre commit hook thinks I touched an encrypted file?**

If the git pre commit hook says there were changes to encrypted files and you didn't touch any file that should be encrypted it means that there is difference in the contents of the plaintext file and what the contents of the decrypted secret file looks like. The pre-commit hook is there to make sure that if you change an API key `secrets.py` and forget to check re-encrypt the `secrets.py` file that it stops you. 