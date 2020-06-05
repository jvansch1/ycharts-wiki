# PyCharm Setup

You can download PyCharm [here](https://www.jetbrains.com/pycharm/).

## Launch PyCharm Project

1. Launch PyCharm
2. Select "Open" 
3. Navigate to `/sites/ycharts`
4. Click "Ok"

## Project Interpreter Setup
For PyCharm to function correctly you really need to setup the project interpreter to use your virtualenv so it can load all the correct packages that our project uses. This will allow you to inspect the source code of any package or 3rd party library.

1. On the main menu, choose `PyCharm | Preferences` Settings.
2. Navigate to `Project:ycharts | Project Interpreter`.
3. Click on the ![Imgur](https://i.imgur.com/fRgFpZz.png) icon in the right hand corner and select `Add...`
4. Then choose the below options.
![Imgur](https://i.imgur.com/3yquPgJ.png)
5. Click "Apply"

## Project Structure Setup
When using all the different search functionality we don't want to include anything in `media`, `node_modules` or `data` directories.

1. On the main menu, choose `PyCharm | Preferences` Settings.
2. Navigate to `Project:ycharts | Project Structure`.
3. Select the `media` folder and on the top bar click "exclude".
![Imgur](https://i.imgur.com/4BY0H7W.png)
4. Click "Apply"

## Python Imports
1. PyCharm by default collapses all your imports which some people like, but you can disable it here (`Preferences | Editor | General | Code Folding`): 
![image](https://user-images.githubusercontent.com/2000316/43075866-1d64f5a6-8e50-11e8-95d1-5beb91c0a7cf.png)

2. You should set your Code Style for Python (`Preferences | Editor | Code Style | Python`) here to sort Python Imports like below. Then click `control + option + o` and it will auto format your imports in the file.

![image](https://i.imgur.com/BzTO1If.png)

3. You should also update your wrapping and indentation settings (`Preferences | Editor | Code Style | Wrapping and Braces`) for imports like this:

![image](https://i.imgur.com/AiWX4Uj.png)


## ESLint Configuration
This is really helpful for spotting Javascript related issues before you get caught by the pre commit hook. 

1. On your host OS X machine (not vagrant) install node via: `brew install node`
2. In PyCharm go to: `Preferences | Languages & Frameworks | JavaScript | Code Quality Tools | ESLint`
3. Copy setting like below
![Imgur](https://i.imgur.com/YxskZEr.png)

## reStructuredText Configuration
This is useful for writing doc strings.
1. In PyCharm go to: `Preferences | Tools | Python Integrated Tools`
2. Copy setting like below
![Imgur](https://imgur.com/k7DXSFf.png)

## Django Setup
Django is very well integrated with PyCharm, including support for Django's test runner and debug server functionality.

### Initial Setup
In the Preferences window (`Cmd + ,` or `PyCharm | Preferences` in the menu bar), navigate to `Languages & Frameworks | Django`. Modify the settings to match the image below:
![Imgur](https://imgur.com/rxhtN4K.png)

### Debug Server Setup
In the Edit Configurations window (`Run | Edit Configurations` in the menu bar), click the Plus symbol in the upper left. Select "Django server", then modify the settings to match the image below:
![Imgur](https://imgur.com/gGQAG6T.png)

The "Environtment variables" text box should contain the following (where `xxxxxx` is the AWS secrets from your local `.env` file): `PYTHONUNBUFFERED=1;DJANGO_SETTINGS_MODULE=ycharts.settings.active;AWS_ACCESS_KEY_ID=xxxxxx;AWS_SECRET_ACCESS_KEY=xxxxxx;AWS_DEFAULT_REGION=us-east-1`

Set any breakpoints you would like in PyCharm by clicking to the left of the line numbers in the file; a red circle will appear next to the line when set. To run the server, select the "Django Debug" configuration from the "Run Configurations" dropdown, then click the "Debug" button.

### Test Runner Setup
In the Edit Configurations window (`Run | Edit Configurations` in the menu bar), click the Plus symbol in the upper left. Select "Django tests", then modify the settings to match the image below:
![Imgur](https://imgur.com/18d0f3E.png)

This will serve as a template for all future Django test cases. To confirm the settings are correct navigate to a test file, then click the green "play" button next to a test class or test method. If the test runner appears and does not raise any unexpected errors, you should be good to go.

NOTE: You can also set breakpoints in tests as in the Debug server. To run tests in debug mode, right-click on the Play button, then select "Debug".