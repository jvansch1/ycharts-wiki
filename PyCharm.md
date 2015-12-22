#### If you have any issues / questions / problems / enhancements. Hit up Tigran or Fox.

# PyCharm Setup

You can download PyCharm [here](https://www.jetbrains.com/pycharm/).

Download the Professional Version and then talk to Fox or Tigran about the 30 day Trial.

## Launch PyCharm Project

1. Launch PyCharm
2. Select "Open" 
3. Navigate to `/sites/ycharts`
4. Click "Ok"

## Import Settings
In order to have the correct code styling by default you will want to install the `pycharm-settings.jar` file.

1. On the main menu, choose `File | Import` Settings.
2. In the Import File Location dialog box that opens select `/sites/ycharts/confs/developers/pycharm_settings.jar`.
3. In the Select Components to Import dialog box that opens specify all the settings to be imported, and click OK.

## Project Interpreter Setup
For PyCharm to function correctly you really need to setup the project interpreter to use your virtualenv so it can load all the correct packages that our project uses.

1. On the main menu, choose `PyCharm | Preferences` Settings.
2. Navigate to `Project:ycharts | Project Interpreter`.
3. Set your Project Interpreter to your virtual env (`~/.virtualenvs/ycharts`)
    * Usually this will already be detected by PyCharm when you go to the dropdown menu
    * ![](http://s1.postimg.org/m5evdzxgv/Screen_Shot_2015_12_22_at_5_33_18_PM.png)
4. Click "Apply"

## Project Structure Setup
When using all the different search functionality we don't want to include anything in `/media/*`.

1. On the main menu, choose `PyCharm | Preferences` Settings.
2. Navigate to `Project:ycharts | Project Structure`.
3. Select the `media` folder and on the top bar click "exclude".
    * ![](http://s8.postimg.org/v5q5wap51/Screen_Shot_2015_12_22_at_5_39_36_PM.png)
4. Click "Apply"