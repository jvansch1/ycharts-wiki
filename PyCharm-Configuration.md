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
![Imgur](https://i.imgur.com/yrzqPUJ.png)

## Project Structure Setup
When using all the different search functionality we don't want to include anything in `/media/*`.

1. On the main menu, choose `PyCharm | Preferences` Settings.
2. Navigate to `Project:ycharts | Project Structure`.
3. Select the `media` folder and on the top bar click "exclude".
    * ![](http://s8.postimg.org/v5q5wap51/Screen_Shot_2015_12_22_at_5_39_36_PM.png)
4. Click "Apply"

## ESLint Configuration
This is really helpful for spotting Javascript related issues before you get caught by the pre commit hook. 

1. On your host OS X machine (not vagrant) install node via: `brew install node`
2. In PyCharm go to: `Preferences | Languages & Frameworks | JavaScript | Code Quality Tools | ESLint`
3. Copy setting like below
![Imgur](https://i.imgur.com/YxskZEr.png)

### Vagrant Configuration
