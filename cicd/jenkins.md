# Tech 258 - Jenkins

- [Tech 258 - Jenkins](#tech-258---jenkins)
  - [Jenkins Overview](#jenkins-overview)
  - [Job Overview](#job-overview)
  - [Job 1 - Jenkins (CI)](#job-1---jenkins-ci)
    - [Steps](#steps)
  - [Configuring Webhook](#configuring-webhook)
    - [Steps](#steps-1)
  - [Testing Webhook](#testing-webhook)
  - [Job 2 - Jenkins (CI-Merge)](#job-2---jenkins-ci-merge)
    - [Steps](#steps-2)
    - [Testing](#testing)
  - [Job 3 - Jenkins (CD)](#job-3---jenkins-cd)
    - [Steps](#steps-3)
  - [Job 4 - Jenkins (CDE)](#job-4---jenkins-cde)
    - [Steps](#steps-4)


## Jenkins Overview
Jenkins is an open source automation server. It helps automate the parts of software development related to building, testing, and deploying, facilitating continuous integration, and continuous delivery.

CICD with Jenkins: <br>
![jenkins.png](images/jenkins.png)

## Job Overview
Every task that is done on Jenkins is referred to as a `job`.
In our case, we will have 3 jobs:
1) **Job 1** - **CI** -> Push event to GitHub Repo will act as a trigger for a webhook to this job -> Builds latest version of app and runs automated tests.
2) **Job 2** - **CI-Merge** -> If the previous job is successful it will trigger this job -> Merge the /dev branch to /main branch.
3) **Job 3** - **CD** -> If the previous job is successful it will trigger this job -> Get the latest code from /main branch and push it to production.
4) **Job 4** - **CDE** -> If the previous job is successful it will trigger this job -> Deploy the latest version of the app automatically.

## Job 1 - Jenkins (CI)
### Steps
1) First login to Jenkins on your Master Node (ec2) to use the Jenkins service.
2) Click `New Item` or `Create new job` (this option will show if the master node is fresh) to start making a job.
3) Enter a `item name` that follows a suitable naming convention. (example: shafique-ci) Select `Freestyle project` for this case. Click `OK` when ready to continue.
4) For the first section (Important fields):
   
- `Description`: Enter description of job (example: Building CI for nodejs app)
- Tick `GitHub Project` and enter `Project URL`. Put `.git` at the end of the URL.

5) For the `Office 365 Connector` section (Important fields):
- Tick `Restrict where this project can be run`
- `Label Expression`: Give the name of our `agent node`. This node will execute the builds of this project. 
**NOTE**: This is a common error you may get below. To get rid of this just get rid of the trailing space at the end of the label and it should recognise the label. Error example: <br>
![common_label_expression_error_example.png](images/common_label_expression_error_example.png)

Example configuration (with common error corrected): <br>
![first_section_and_office_365_connector_section_example.png](images/first_section_and_office_365_connector_section_example.png)

6) For the `Source Code Management` section (Important fields):

- Select `Git` as that's what we are using for SCM.
- `Repository URL`: Enter the SSH URL for the repo, this can be found on your GitHub Repo by clicking the `Code` button and going to the `SSH` tab of the `Clone` section.
**NOTE**: This is a common error you may get below. This is because Jenkins is trying to ping our repo but it doesn't have permissions due to us setting up SSH. Once we give it the corresponding private key in the `Credentials` tab (next step), this error should resolve itself. Error example: <br>
![common_credentials_error_example.png](images/common_credentials_error_example.png)
- `Credentials`: As we have previously assigned a public key to our repo, we need to give Jenkins the corresponding private key to authenticate with our repo. We therefore have to click `Add` and put in our private key there.
Example credentials configuration: <br>
![credentials_example.png](images/credentials_example.png)
- `Branches to build`: Update `Branch Specifier` to `*/main` as that is the master branch of our repo.

Example configuration (with common error corrected): <br>
![scm_section_example.png](images/scm_section_example.png)

7) For the `Build Environment` section (Important fields):
- Tick `Provide Node & npm bin/ folder to PATH`
- `NodeJS Installation`: Select `Sparta-Node-JS`. This has been set up for us to use already! This will also fill out the `npmrc file` and `cache location` fields.

Example configuration: <br>
![build_environment_section_example.png](images/build_environment_section_example.png)

8) Click `Save` to complete our job configuration.
9) Now we are ready to manually run our job. Click `Build now`. This will run our job. If everything is successful our job in `Build History` will have a blue orb meaning success. To view logs, click the `#1` and select the `Console output` option. Examples: <br>
![build_now_example.png](images/build_now_example.png)
![build_now_console_output_example.png](images/build_now_console_output_example.png)

We can see that our job has been run successfully! <br>
![job_success_example.png](images/job_success_example.png)

## Configuring Webhook
Now that we have our job configured and tested manually, we can set up a webhook to automatically trigger the job whenever our repo is updated (i.e. a new push event has occurred to our repo). We can set up us Webhook by doing the following:

### Steps
1) Go to our CICD App Repo on GitHub.
2) Go to `Settings > Webhooks (Under Code and Automation Section)`
3) Click `Add Webhook`
- `Payload URL`: This is the URL of our Jenkins Master Node. Example URL: 
```
http://<public_IP_of_jenkins_master_instance>:8080/github-webhook/
```
- `Content type`: application/json
- `Which events would you like to trigger this webhook?`: We can select `Just the push event`. If in future we would like for our Master Node to pull we can select `Let me select individual events` and select `Pull requests` and `Push`. Example: <br>
![webhook_events_example.png](images/webhook_events_example.png)
4) Click `Add Webhook` when ready.

## Testing Webhook
Once the Webhook is created, a test ping will occur. If successful you may see a tick next to your Webhook under the `Webhooks` tab of our GitHub Repo settings. Example: <br>
![](images/webhook_ping_example.png)

Another way we can test our webhook is by pushing new content to our repo. For example, we can update the contents of our README.md file and push those changes to GitHub.

**NOTE**: we have to update our job configuration to take the newly made Webhook into account on Jenkins too:
- Go on the Jenkins job and click `Configure`
- Go to the `Build Triggers` section and tick `GitHub hook trigger for GITScm polling`.

Now that we have the webhook set up, a new build should occur and this can be seen on the `Build History` on Jenkins.
Example: <br>
![](images/webhook_build_triggered_example.png)

As we can see our build has triggered a second time (#2). We have successfully configured and tested our webhook!

## Job 2 - Jenkins (CI-Merge)
**NOTE**: Before creating this Job we have to update Job 1 to consider a few things:
- Change `Branch Specifier` in `SCM` section to `*/dev` as we now want Jenkins to listen to changes on the `*/dev` branch now.

### Steps

### Testing
As we want Job 2 to trigger automatically after a successful Job 1:
- Revisit Job 1's configuration
- Go to `Post-build Actions` section and `Add post-build Action`.
- Select `Build other projects` and enter name of `Job 2` in `Projects to build field`.


## Job 3 - Jenkins (CD)
### Steps
1) Create an App EC2 instance. Use `Ubuntu 18.04` as Image.
2) Add `SSH Agent` in `Build Environment` section. For `Credentials` insert the contents of `tech258.pem` (private key) so Jenkins can SSH into our App EC2 instance to set up our app.

## Job 4 - Jenkins (CDE)
### Steps