This a guide walking through anyone who wishes to upgrade from Mojave to Catalina. There are 4 things to be aware of:
1. The default shell for all _new_ accounts is `zsh` vs `bash`. If your current account is using `bash` it will stay as `bash` but just be warned all new YCharts engineers will be using `zsh` and if you decided to "re-install" to update by wiping your computer clean and starting fresh it will change your shell to `zsh`.
2. There is a bug in `autoenv` when installed via `brew` so we need to re-install `autoenv` via git.
3. The biggest change of all is that the root directory is no longer writable so we cannot have a folder `/sites` like we currently do. The fix to this is to create what is called a "firmlink" (essentially the same thing as a symbolic link except it is bidirectional) on the root directory so things that depend on `/sites` directory still function properly. If you want to read more about this and why Apple did this here is a good [article](https://arstechnica.com/gadgets/2019/10/macos-10-15-catalina-the-ars-technica-review/11/) and here is Apple's [PDF](https://devstreaming-cdn.apple.com/videos/wwdc/2019/710aunvynji5emrl/710/710_whats_new_in_apple_file_systems.pdf)
4. The last change (which should have already happened before updating) is that Docker cannot recognize this firmlink so we have to update the settings there.


## 1. Reinstall Autoenv
> NOTE: We are re-installing Autoenv via github because there is a bug in Catalina that is fixed on the master branch that has not made its way to `brew` yet.
```
# Uninstall autoenv from brew
brew uninstall autoenv

# Remove activation of autoenv from login shell profile
sed -i. '/source \/usr\/local\/opt\/autoenv\/activate.sh/d' ~/.bash_profile

# Install autoenv via github
git clone git://github.com/inishchith/autoenv.git ~/.autoenv

# Add back activation of autoenv to login shell
echo 'source ~/.autoenv/activate.sh' >> ~/.bash_profile

# Source profile
source ~/.bash_profile
```

## 2. Upgrade OS to Catalina
1. Go to Systems Preferences -> Software Update
2. Follow the upgrade instructions to upgrade to Catalina


## 3. Setup `sites` directory
> NOTE: What we are doing here is moving your old `/sites` directory to `~/sites` and then creating a firmlink. When upgrading, Catalina moves all root directories to `/Users/Shared/Relocated Items/Security/`. Once the firmlink is created we need to restart the machine.

Once the upgrade is complete, login and open the terminal and copy and paste the following:
```
rm -rf ~/sites
sudo mv /Users/Shared/Relocated\ Items/Security/sites ~
sudo touch /etc/synthetic.conf
printf "sites\t/Users/`whoami`/sites\n" | sudo tee /etc/synthetic.conf
sudo shutdown -r now
```

## 4. Reprovision Vagrant & Install XCode
Once the computer restarts, open the terminal and copy and paste the following:
```
cd /sites/ycharts
xcode-select --install
rm -rf node_modules
vagrant destroy
vagrant up --provision
```

## 5. Test setup
Once these steps are complete run the local django server through vagrant, along with the chart generator and ensure it is working. You should also test that you are able to SSH into staging machines through the `ycharts_systems` repo.