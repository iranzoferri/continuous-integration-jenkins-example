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




![](img/.png)

![](img/.png)

![](img/.png)
