## Getting started with sublime

## Essential Commands ##

**Shift-Command-P**
    This is the "Command Palette". It is a quick way to execute commands w/o using a mouse. Most extensions you
    install will add new commands to the command palette for easy access.

**Command-P**
    Find file autocompleter. This will open a file via an autocomplete search on your project.

**Command-F**
    Find text in file

**Option-Command-F**
    Find/replace text in file

**Shift-Command-F**
    Find/replace text in Project/directory/open files

    Cool note here: If you're getting way many results from Migrations, json, and onetime scripts, you can exclude those folders from the search.

    In the "Where:" box, you just type:

        /sites/ycharts/apps/,-/sites/ycharts/*/migrations/*,-/sites/ycharts/*/onetime_scripts/*,-/sites/ycharts/apps/calculations/calc_info/*.json,-/sites/ycharts/apps/calculations/static/calculations/*.json,-/sites/ycharts/apps/support/static/support/*.json

    The minus operator (-) in front of the relative paths excludes those directories from searches.

**Command-G**
    Go to line

**Command-D**
    When selecting a phrase, this will add to the selection the next place this phrase appears. If you
    start typing, it will edit all at once!

**Command-Option-2**
    Split screen

**Comand-Option-1**
    Single screen

**Escape**
    Go from "insert" mode, to "command" mode. In command mode, various keyboard keys do different things.
    Don't know much about this yet.

**i**
    Go from "command" mode, to "insert mode". Insert mode is where you are when you are editing files.

**Command-/**
    Mass comment or uncomment

## Essential Plugins ##

Before you do anything else, install "Sublime Package Control", which will make installing other packages
much easier. Instructions are here: http://wbond.net/sublime_packages/package_control/installation

Once that is in, then press Command-Shift-P to bring up the command pallete and then type "install package" to
pring up package installation menu and press enter. Now, these are nice packages to have.

**Djaneiro**
    Django syntax highlighting.

**Git**
    Git magic (haven't used much).

**SublimeLinter**
    This will lint Python, JS, CSS files so they are compliant. The CSS linter really doesn't like
    our CSS. For the time being, when editing CSS you can run the command "SublimeLinter: Disable Linting"
    to turn off temporarily

    PEP8 has a few checks that we don't use. To disable them, go to Preferences -> Package Settings -> SublimeLinter -> Default
    and you'll see a PEP8 ignore list that you can add "E128" and "E501" to.

**TrailingSpaces**
    Once you have linter, you might see alot of trailing space errors in Python files. With this installed,
    run the command "Trailing Spaces: Delete Trailing Spaces" to remove trailing spaces from a file.

**Markdown Preview**
    To look at our .md docs (like this file!) all sexy like, install this and then run the command
    "Markdown Preview: current file in browser". Wow, isn't that cool.