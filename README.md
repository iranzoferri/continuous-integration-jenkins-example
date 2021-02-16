# CI WITH JENKINS ♾️

Prerequisites:
- docker
- docker compose

### BUILD AND START

There are two files Dockerfile and docker-compose.yaml

With this command the image will be built in few minutes:

```bash
docker-compose build
```

You can show with this:

```bash
docker image ls | grep -E '(blue|jenkins)'
```

Start the container:

```bash
docker-compose up -d && docker-compose logs -f
```

You need to look at the logs because the password will be displayed here, at the end of start, like this:

```
Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

************************** <-- hidden here, sorry xD

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword
```

If you launch with yaml especification continue on the next step, but if you have a service on other host and another port, ergo docker executes in a server on your lab or some thing like (test environment). Then, you can access with to this with the ssh command:

```bash
# ssh -p [port ssh server] -L [external open port on server]:localhost:[docker service port] [user]@[host]
example: ssh -p 22 -L 9006:localhost:32768 user@yourhost.es
```

Next, put http://[host]:9006 in your browser.

Click on "Install suggested plugins" wait until process is complete, then create a new "admin" account:

![jenkins_create_admin](img/jenkins_create_admin.png)

If you need to change the language, go to "Manage Jenkins" > "Manage Plugins" and click on "Available" tab, search and install "Locale" plugin.

A new entry apears into "Manage Jenkins" > "Configure system" named Locale:

![jenkins-locale-config](img/jenkins-locale-config.png)

Is time to create a new job:

![jenkins-create-job](img/jenkins-create-job.png)

and continue with "Freestyle project"

![jenkins-job-item](img/jenkins-job-item.png)

Click "Ok" and go to repository [example-voting-app](https://github.com/dockersamples/example-voting-app), then fork it to yours.

![git-fork](img/git-fork.png)

When fork is finished, copy "HTTPS" link on your repository, not "SSH", be careful:

![git-clone-repository-link](img/git-clone-repository-link.png)

Fill in all these fields in Jenkins job:

![jenkins-job-paste-clone](img/jenkins-job-paste-clone.png)

and lower:

![jenkins-job-build-commands](img/jenkins-job-build-commands.png)

Trigger the job by clicking here:

![jenkins-job-build-now](img/jenkins-job-build-now.png)

Look the progress bar, if you will click on it, will show you the "Console Output":

![jenkins-job-build-output](img/jenkins-job-build-output.png)

### MAVEN JOB

First, install Maven plugin named "Maven integration" on Jenkins.

Next, go to jobs, name it and select "Folder" and click "Ok"

Inside the job folder, click on "Create a job"

![jenkins-job-folder](img/jenkins-job-folder.png)

![jenkins-job-worker-maven](img/jenkins-job-worker-maven.png)

and clik "Ok".

Put a description and repo url like the previous project:

![jenkins-job-paste-clone](img/jenkins-job-paste-clone.png)

Because pom file is on a subfolder, change this here:
![jenkins-job-maven-directory](img/jenkins-job-maven-directory.png)

Now, you can trigger build process:

![jenkins-job-maven-build-now](img/jenkins-job-maven-build-now.png)

and look "Console Output"

![jenkins-job-maven-build-success](img/jenkins-job-maven-build-success.png)


### UNIT TEST

Now we go to make a unit test worker on Jenkins, to do this we create a copy of previously worker job named "worker-build".


![jenkins-job-maven-item-name](img/jenkins-job-maven-item-name-test.png)

![jenkins-job-maven-copy-from](img/jenkins-job-maven-copy-from-build.png)

and click "Ok".

Leave everything the same, except the description (type whatever you want) and build step like this:

![jenkins-job-maven-clean-test](img/jenkins-job-maven-goal-clean-test.png)

Again, click on build Job:

![jenkins-job-build-now-button](img/jenkins-job-build-now-button.png)

### PACKAGING

Now we go to make a package worker on Jenkins, to do this we create a copy of previously worker job named "worker-test".

![jenkins-job-maven-item-name-package](img/jenkins-job-maven-item-name-package.png)

![jenkins-job-maven-copy-from-test](img/jenkins-job-maven-copy-from-test.png)

On next step, do the same as in the previous step, put a description and leave everything the same, except "Build > Goals" (see image) and click on the "Save" button.

![jenkins-job-maven-clean-test](img/jenkins-job-maven-goal-package.png)

Go to example-voting-app-job folder:

![jenkins-job-folder-list-1](img/jenkins-job-folder-list-1.png)

Click on the bar and look the console:

![jenkins-job-folder-build-success](img/jenkins-job-folder-build-success.png)

Go back and this is the result:

![jenkins-job-folder-list-2](img/jenkins-job-folder-list-2.png)

Now, go to "worker-package" job located at "example-voting-app-job" folder and click on "Workspace", inside the tree, go to worker/target/" and look here, te "jar" file is stored.

![jenkins-job-package-jar](img/jenkins-job-package-jar.png)


### Archive the artifacts

Will make a few changes to the "worker-package" job:

![jenkins-job-package-build-delete](img/jenkins-job-package-build-delete.png)

Add option "skipTest" with "-D" argument, see help:

![jenkins-job-package-skip-test](img/jenkins-job-package-skip-test.png)

And put a match expression to archive the "tar" files (see help):

![jenkins-job-package-archive-artifacts](img/jenkins-job-package-archive-artifacts.png)

Save and click on "Build Now" again.

This is the result:

Console log:

![jenkins-job-package-console-archive](img/jenkins-job-package-console-archive.png)

and job (new section appears):

![jenkins-job-package-new-section-artifacts](img/jenkins-job-package-new-section-artifacts.png)

You can download "tar" from here.

### BUILD TRIGGERS

Trigger builds remotely (e.g., from scripts)

Go to user management section and click on gear button of the your user (The user 
what do you wants to trigger the action):

![jenkins-user-configure-admin](img/jenkins-user-configure-admin.png)

With this we can add a token to securely trigger the actions:

![jenkins-user-new-token-test](img/jenkins-user-new-token-test.png)

The tokens only appears once for security reasons (see help), and don't do that:

~~11cab52627677978bf9758e1342bb2d530~~
~~11a94d8c0a33ae3e782de6384d5bc1494f~~

*Tip: use a token for each thing and if there is suspicion, renew it immediately, depending of its criticality.*

![jenkins-job-trigger-builds-remotely](img/jenkins-job-trigger-builds-remotely.png)

This is the URL resultant, with this you can trigger the action from a scripts remotely:

```bash
curl http://admin:11a94d8c0a33ae3e782de6384d5bc1494f@192.168.123.123:9006/job/example-voting-app-job/job/worker-package/build?token=11cab52627677978bf9758e1342bb2d530&cause=Test+This%20is%20to%20test%20the%20remote%20trigger
```

Put it on your browser or exec a curl command and appreciate the origin of the trigger on the console output:

![jenkins-job-console-remote-trigger](img/jenkins-job-console-remote-trigger.png)


On "Display options" you can select amount of "Displayed Builds", I put "5" on "No Of Displayed Builds".

### PIPELINES

The next part is configure a pipeline, for this is recomended to install "Build Pipeline" plugin:

![jenkins-plugins-pipeline](img/jenkins-plugins-pipeline.png)

Later, we will see how to update jenkins to avoid this security isues. 

And now, click on "+" simbol into the job folder,

![jenkins-job-folder-plus](img/jenkins-job-folder-plus.png)

this is for configure a new view, type the name,

![jenkins-job-build-pipeline-view](img/jenkins-job-build-pipeline-view.png)

and set "Pipeline Flow",

![jenkins-plugin-pipeline-flow-layout](img/jenkins-plugin-pipeline-flow-layout.png)

finally will generate this view:

![jenkins-job-build-pipeline-build-view](img/jenkins-job-build-pipeline-build-view.png)


but... need to set upstream and downstream to make the proper correlation, like this:


    build >>> test >>> package

So, go to "worker-build" job config and set new build trigger:

![jenkins-job-worker-build-post](img/jenkins-job-worker-build-post.png)

now go to "worker-package" job config and set "Build Triggers", check "Build after other projects are built" and type here "worker-test"

![jenkins-job-build-trigger-after](img/jenkins-job-build-trigger-after.png)

and you can see the up/downstream, this is the relationship of the processes in your pipeline,

![jenkins-job-up-down](img/jenkins-job-up-down.png)

but there is still more, with the pluggin this is more visual:

![jenkins-job-view](img/jenkins-job-view.png)

See in action:

![jenkins-job-pipeline-animation](img/jenkins-job-pipeline-animation.gif)

Sorry, the update doesn't work very well and I accidentally launched it twice xD




![](img/.png)