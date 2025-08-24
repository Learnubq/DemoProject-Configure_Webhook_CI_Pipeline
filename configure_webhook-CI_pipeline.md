## Demo Project - Configure Webhook to Trigger CI Pipeline Automatically on Every Change

This demo shows the process of configuring a webhook to trigger a CI Pipeline to run automatically, every time a new change is pushed to the GitLab repository.

## Steps to Configure Webhook to Trigger CI Pipeline Automatically on Every Repository Change Pushed

**One situation where you would not want the Jenkins CI pipeline to trigger automatically on every new commit pushed to the remote repository, is when the application is the production type - you would want to manually trigger the cI pipeline after quality and risk checks of the commit**

**Another way to trigger CI pipeline builds is by scheduling them e.g. every 2 hours or every day at a specific time (for example, if you have very intensive tests, like Selenium, or end to end tests that take 2 hours). In this situation you won't want to run these 2 hour tests every day as you would overload the Jenkins server (due to the automatic CI pipeline builds being run on every commit)**

    **So you should schedule the tests to run at night when nobody is working, every single day**
    **Another use case of this is for long running job that maybe cleans up some stuff on Jenkins**

1. **I configured Jenkins so that every time I pushed a commit to the GitLab repository, the CI pipeline build started automatically. To do this I needed to use Jenkins plugins**

**First, I went to "manage Jenkins" then plugins in Jenkins UI. Then I installed the gitlab plugin, and after the install I navigated to "manage Jenkins" and then "system". I then scrolled down to the Gitlab connections section (by default it is enabled). The Gitlab host URL is the URL where Jenkins will get notifications from Gitlab that something changed in the repository. I completed the Gitlab connections form.**

**For the GitLab Host URL I was hosting the application on a GitLab repository, however, in a company you may have to host GitLab on the company's own server - so If you have GitLab in your own company, the URL for GitLab Host will be different. In my case the URL was just "https://gitlab.com/"**

**For the API token for accessing Gitlab I needed to create one by clicking on the shortcut "Add" button and click on Jenkins, to create one for the Jenkins scope**

    **For "kind" I selected "Gitlab API token"**

    **I then generated and copied an API token on Gitlab. I added it to the form and added an ID called "gitlab-token"**

**I then selected the gitlab token in the Credentials dropdown and clicked save. This configured Jenkins to authenticate with Gitlab and talk to its API**

2. **I then clicked the "my-pipeline" pipeline job I created and clicked "configure". In the Gitlab connection section I saw the "gitlab-connections" API that I had just set up* through the gitlab plugin I installed**

**In the same section, under the "Build Triggers" section I clicked "Build when a change is pushed to Gitlab. Gitlab webhook URL: http://165.232.114.245:8080/project/my-pipeline. In the subsection "Enabled Gitlab triggers", I checked "Push Events" and "Opened Merge Request Events". This meant Jenkins would accept notifications from Gitlab for push events or merge request events - the build for that pipeline would then be triggered automatically (whenever Gitlab sends Jenkins a notification that a push or merge request event happened in the repository, and it sends it on the given Jenkins URL)**

**I clicked save**

3. **I then had to configure Gitlab to automatically send a notification to Jenkins whenever a commit or push happened**

**I navigated to the project's settings in Gitlab and clicked the "integrations" sub-link**

**I then scrolled down until I saw Jenkins and clicked on it. I clicked "Active" for "Enable integration" and "Push" under "Trigger. For the Jenkins server URL I used "http://<Jenkins IP address>:8080"**

    **If runnig Jenkins on a localhost, that Jenkins URL won't work - Gitlab integrations does not allow localhost as an option there - it has to be a real IP address or domain name**

**I ensured the "SSL verification" option was unchecked**

**I then had to configure the project name e.g. the CI pipeline job name inside Jenkins, which was "my-pipeline"**

**I then added the username and password for Jenkins and clicked save - there should be a green tick by "Jenkins" after saving. I clicked the "test settings" option to ensure it said "successful"**

4. **I then built the "my-pipeline" job and it was successful**

5. **I then edited the Jenkinsfile in the "feature/payment" branch to check if the automatic build would occur and it did - the build number had increased to #26 and was a complete build**

6. **I then went to set-up the automatic Gitlab push for the multi-pipeline job, but it was not as easy a set-up as for the single pipeline job. The gitlab plugin does not work for the multibranch pipeline. I needed to install a different plugin called "Multibranch Scan Webhook Trigger"**

**After this I didn't need to configure the system settings for it like I did for the single pipeline, instead I returned to the multi-branch pipeline and went to configure settings. Under "Scan Multibranch Pipeline Triggers" and clicked "Scan by webhook". This option asks for a "Trigger token" of whatever we want e.g. "gitlabtoken". This token will be used for the communication betweeen gitlab and Jenkins**

**To use the token I had to configure Gitlab - I went to the project settings and clicked "Webhooks" - I then had to tell Gitlab to send Jenkins a notificaton on a specific URL using the token "gitlabtoken". When Jenkins receives that token notification it would trigger the build job for the multibranch pipeline. On the URL Jenkins would accept calls from Gitlab - Gitlab would send a notification to that URL and the token will be sent with that request**

**I saved the multibranch configuration**

**I then went back to the Gitlab Webhook settings and clicked "push events" and "all branches". I then clicked "add webhook"**

**I then tested the automatic build trigger worked by changing the project code - it was successful as the 5th build was triggered and successfully completed**
