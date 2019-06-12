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
![image](https://user-images.githubusercontent.com/2000316/43075802-df90449c-8e4f-11e8-9d1f-529b286af5ea.png)

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