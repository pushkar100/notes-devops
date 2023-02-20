# Jenkins (2023)

- [Jenkins (2023)](#jenkins--2023-)
  * [Why would you use Jenkins?](#why-would-you-use-jenkins-)
  * [Alternatives to Jenkins](#alternatives-to-jenkins)
  * [Drawbacks of Jenkins](#drawbacks-of-jenkins)
  * [Terminology](#terminology)
    + [Jenkins specific terminology](#jenkins-specific-terminology)
  * [Step 1: Hosting Jenkins](#step-1--hosting-jenkins)
  * [Step 2: Hosting the application](#step-2--hosting-the-application)
    + [Adding Git to the server](#adding-git-to-the-server)
  * [Step 3: Setting up Jenkins](#step-3--setting-up-jenkins)
  * [The Jenkins dashboard](#the-jenkins-dashboard)
    + [Manage Jenkins dashboard](#manage-jenkins-dashboard)
  * [Step 4: Installing plugins](#step-4--installing-plugins)
    + [Blue Ocean](#blue-ocean)
  * [Step 5: Creating a pipeline](#step-5--creating-a-pipeline)
    + [Creating a stage](#creating-a-stage)
      - [Example JenkinsFile in your repo](#example-jenkinsfile-in-your-repo)
    + [Example of adding a shell command as a step in a stage](#example-of-adding-a-shell-command-as-a-step-in-a-stage)
  * [Step 6: Editing a JenkinsFile](#step-6--editing-a-jenkinsfile)
  * [Running stages and steps in parallel](#running-stages-and-steps-in-parallel)

**Jenkins is a tool for automation with Continuous Integration (CI) and Continuous Deployment (CD) pipelines.**

* Started in **2004**
* Completely **open source**
* Most commonly used automation server in software development

## Why would you use Jenkins?

Jenkins has the ability to watch for events in your repository and react to them.
The reactions can, to list a few examples, be:
* Build your code
* Run scripts
* Execute tests
* Deploy your code if your tests pass
* Reject the build if your tests fail

Jenkins also has the following benefits:
* It can run your code in a single environment ensuring consistency as well as in multiple defined environments
* It has a great community offering tutorials and Q&As
* It is based on a plugin system. You will most likely use them for compiling testing, etc (Ex: Docker and Git plugins)
* It is self hosted by default (No enterprise build, free) and has been proven for scale i.e also being used by large companies
* It has many options for configuration
* Improves software development cycle i.e faster and more performant

## Alternatives to Jenkins

1. **Travis CI**
2. **Circle CI**
3. **Github Actions**
4. **Gitlab CI**


## Drawbacks of Jenkins

* UI is a bit outdated. This can be fixed with a newer UI available via the Blue Ocean plugin (New UI for most things but not all)
* Community driven makes the plugin usage a bit tricky since we need to watch out for bugs, confusing names, and unmaintained code
* It has been around for so long that you need to make sure that the documentation you are reading is not stale i.e for a previous version
* You are responsible for deploying it, securing the pipelines and so on

## Terminology

1. **Continuous Integration (CI)**: Integration of code from multiple contributors into a single software project. 
  * Allows developers to merge their code into a central repository where builds and tests run
  * Automated tools test the correctness of the code before integration
  * It is a pre-cursor to CD
2. **Continuous Deployment (CD)**: Responsible for packaging the app and getting it ready for deployment using automated build tools
  * We can deploy all code changes to a testing and/or production environment after the build stage
3. **Pipeline**: The entire CI/CD flow is known as a pipeline. It is a series of steps that:
  * Build
  * Test, and 
  * Deploy automatically to environments

### Jenkins specific terminology

1. **Controller**: The main instance of Jenkins that is running. It manages configurations, keys, plugins, and coordinates the other agents
2. **Agents**: These are containerized environments that will run your pipeline steps (known as **Jobs**) i.e if some job (task) is to be done then Jenkins assigns it to an agent. The agent can run till completion (failure / success) and you can even configure it to keep running instead of spinning one up for every job.

## Step 1: Hosting Jenkins

Since Jenkings is an open souce software that relies on being self-hosted, we need to install it on a server. 
The typical step is to create an account on a hosting provider and then install Jenkins there (usually by installing the provided Jenkins docker container).

As an example, Linode (By Akamai - $100 credit for the first 60 days) is a Linux hosting service provider which offers a “marketplace” product “Jenkins” that will one-click install Jenkins for you on a server. 
* It will ask you for options such as which linux distro to install the tool on and a region.
* Provide a label
* Provide a root password (or) an SSH key (so that you may login later)
* Create it!

## Step 2: Hosting the application

We need to host the application somewhere on its own server. This is where the production code will live. Ex: A website for your company’s product.
Follow the same steps provided by a hosting service to deploy your app. It is common to use a Docker containers as a modern way of packaging your code and the environment on the server. 

As an example, use a Linode server space to manually install Docker - or better, choose from their market place to spin up one (do not choose any advanced options).

### Adding Git to the server

If your Jenkins server instantiation came with a Git installation as per the set up (or if you manually installed it) then that’s good! We need git since our repos will be used as the source code and it is where our JenkinsFile resides. Basically, Git is very important. You can SSH into your Jenkins server and install Git.

Example of installing Git on your Jenkins Linode server using their web console:
* On Linode, you can install git by going to your server dashboard > selecting “Launch LISH console” > Login with “root” and password for the Jenkins server (Successful SSH).
* Run `apt upgrade` followed by `app install git`
* Verify with `git --version`

## Step 3: Setting up Jenkins

Once Jenkins is installed i.e a server running Jenkins is set up, we need to get it up and running (set up Jenkins with an admin user)

1. The Jenkins server must have an associated IP address in the (Linode) dashboard for it. Ex: `178.79.179.236` 
2. **Jenkins set up wizard**: This IP on port `8080` will allow you to open it on Jenkins web browser. First time interaction prompts you to SSH into the Jenkins server and copy a password from a particular file. Ex: `/var/lib/jenkins/secrets/initialAdminPassword`
3. We can SSH login to this server like so: `ssh root@178.79.179.236` (Should be available on the dashboard for managing the server i.e on Linode)
4. Copy the password from the file and input it on the webpage
5. Install the suggested plugins when Jenkins prompts you
6. **Admin step**: Create a Jenkins admin (with username and password) that you will later login to manage the tool and view the Jenkins UI (**Note**: this is not the same as the password for the Jenkins’ server - that one is just a password to ssh into it, not part of Jenkins itself)

Save the admin credentials safely!

Optionally, you may be asked to map to a domain name which you can do so if you have one for it, specifically. Just the IP should be fine, otherwise.

## The Jenkins dashboard

**Top-right:**
* User profile navigation
* Alerts and erro notification icons
* Search bar

**Top-left:**
* Jenkins logo

**Left (navigation bar):**
* “New Item": Whenever we create a new project or a pipeline, etc. We will use this link to open up the dashboard for creating it.
* “People": Opens a dashboard to add and manager users and their permissions. Ex: Users with different privileges like for devops, dev, etc.
* “Build History": Link to a dashboard that lists the info on the previous builds that we have created as part of any pipeline.
* “My Views": Lists all the views we have for a job. We can also create a view for different types of jobs and also for different types of users.
*  **“Manage Jenkins"**: Links to a dashboard for adding and managing our Jenkins setup including plugins.

### Manage Jenkins dashboard

* Can **"Configure System"** which lets you set the options for the core of Jenkins and some plugin settings.
* Can **"Manage Plugins"** which lets you search for, install, and manage plugins.
* Can **"Manage Nodes and Clouds"** is responsible for managing agents / nodes
  *  The **"Built-in Node"** is the **controller** of Jenkins i.e the main node. We can run jobs here but it is usually used for instructing other agents i.e new nodes which do the actual job running
* Can **"Manage credentials"**: When we create new nodes / agents that run jobs and we want to give people access to them, we use this space to create and manage that access.

## Step 4: Installing plugins

* This can be done by going to the **Manage Jenkins > Manage Plugins** dashboard
* Here, we can see the Available and Installed plugins. Go to **Available** and search for your plugin
* Select the plugin(s) and click on either "Install without restart" or **"Download now and install after restart"** (recommended if you are going to use that tool before doing other things - will need to check the box that says "Restart Jenkins when installation is complete and no jobs are running” on the next step)
* You will have to login after the restart using the admin credentials

Plugins and their dependent plugins are installed (if not already present)

Popular plugins to install are:
1. Blue ocean (for a modern Jenkins UI)
2. Docker
3. Docker Compose Build Step

### Blue Ocean

Blue ocean provides a modern UI for Jenkins jobs
* Left bar: Contains the blue ocean navigation link
* Clicking on it takes you to a new interface for creating pipelines
* Going to other links such as profile, home page, or Manage Jenkins will restore the old UI as blue ocean does not support those dashboards

## Step 5: Creating a pipeline

1. Go to Blue Ocean
2. Select **"Create a pipeline"**
3. It will ask you which git hosting provider do you use for your code. Ex: Github, Gitlab, BitBucket, etc. Select the appropriate one
4. **Access token**: Go to Github (git hosting) using the link provided or to Setting > Developer Settings to create an access token
5. Submit the access token on Jenkins
6. It will ask you to select the **Organization** you belong to (Select the appropriate one i.e one where the code repository lives)
7. Pipeline is created!

Once the pipeline is created, an **interactive interface** with nodes and edges should greet us (“start” node attached to a plus + symbol)

A pipeline needs the following:
1. **A Jenkins file**: This is a file that lives in the repository that we are targeting for the automation. It is a configuration file for a pipeline. It lists the stages in a pipeline and what needs to be done at each step i.e we write code for the stages that happens from **top to down**
  * This file is auto generated and committed into the repo when the pipeline gets saved. Since it is in the repo, it can be updated, versioned, rolled back and so on. 
3. **Stages and steps**: These are the nodes that we see in the UI. We can add one, delete it, and so on. Every circle is a **stage** and a stage contains **steps**. Steps are the places where we run our scripts, calls to Jenkins’ plugins, etc i.e code for automation. These can even run in *parallel*
  * Naming a step in the blue ocean pipeline: Click on the node and write the name
  * Every stage has to have at least one step!

### Creating a stage

In the Blue Ocean UI:
1. Create a new stage by clicking the plus `+` button i.e a node
2. Provide a name
3. Select a script / plugin to run. Ex: Git
4. Provide the URL to the git repo. Ex: https://github.com/pushkar100/curriculum-app
5. Provide the branch name. Ex: dev
6. Click “Save"
7. It mentions that a **JenkinsFile** is going to be created in the branch specified (or you can ask it to create a new branch). This is what we want.
8. It runs the pipeline… if the plugins exist or the script runs successfully, no errors should be there. Else, fix the script or plugin

**Note: **Run the pipeline again if you fixed an error by clicking the ⟲ re-run icon at the top. Pipeline turns from ‘red' to ‘green’ as it successfully executes.

*Red*: Failure
*Yellow*: Warnings / certain test failures
*Green*: Success

#### Example JenkinsFile in your repo

```
pipeline {
  agent any
  stages {
    stage('Checkout Code') {
      steps {
        git(url: 'https://github.com/pushkar100/curriculum-app', branch: 'dev')
      }
    }

  }
}
```

The above lists a step that is using a git plugin that uses the git tool installed on the Jenkins server to checkout to a branch.

“Added JenkinsFile” is the default commit message when you create and save a pipeline for a repo that does not contain it.

### Example of adding a shell command as a step in a stage

1. For a new stage, give it a name, and select shell script option
2. Enter a shell command. Ex: `ls -la` 
3. Save with a description (this will be used as the commit message)
4. Jenkins runs and you will see this pipeline echo a console output with the list of files in the root drectory

```
stage('List files') {
  steps {
    sh 'ls -la'
  }
}
```

## Step 6: Editing a JenkinsFile

From the UI
1. Go to **Branches** for a pipeline
2. Hover over a branch and select **Edit**
3. You should see the interactive pipeline editor

## Running stages and steps in parallel

You can run it scripts/plugins in parallel.
Ex:

```
pipeline {
  agent any
  stages {
    stage('Checkout Code') {
      steps {
        git(url: 'https://github.com/pushkar100/curriculum-app', branch: 'dev')
      }
    }

    stage('List files') {
      parallel {
        stage('List files') {
          steps {
            sh 'ls -la'
          }
        }

        stage('Frontend unit test') {
          steps {
            sh 'cd curriculum-front && npm i && npm run test:unit'
          }
        }

      }
    }

  }
}
```

**Note**:
* The above “parallel” code could run in a single stage but running two steps in parallel as well!
* Since the above code executes `npm` commands, `node` and `npm` have to be pre-installed on the Jenkins server (Else, install them and re-run pipeline)


This covers the very basics of what you can do with Jenkins



