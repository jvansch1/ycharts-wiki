### How to run a Travis build in Debug mode
[Official docs](https://docs.travis-ci.com/user/running-build-in-debug-mode/)

Sometimes, it can be helpful to debug a Travis build, e.g. when tests are passing locally but failing on Travis. If you are trying to debug a Django test case, start off by adding an `import ipdb;ipdb.set_trace()` where you need it and pushing it

NOTE: If tests are passing locally but failing on Travis, first try merging develop into your branch. If tests are still passing locally, proceed

1. View the Travis build for your pull request
2. Press the `Debug build` button
3. After a while, Travis will provide a ssh key. ssh into the machine

At this time, the build is paused at the beginning of the `before_install` step (even though we don't have that step defined). First, we need to give the root user the correct PATH 

4. `echo $PATH`
5. copy the output
6. `sudo su`
7. `export PATH=<paste output>`

Now, you can copy and paste commands from the `install`, `before_script`, and `script` sections of our `travis.yml` file as necessary. 

NOTE: When running installing `requirements.txt`, it may appear stuck on installing bottleneck, but just be patient!



