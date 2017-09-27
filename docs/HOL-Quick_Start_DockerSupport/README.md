HOL - Quick Start Docker Support
====================================================================================
In this lab we have an application called PartsUnlimited. We want to set up Docker support with Visual Studio Team Services (VSTS). 

## Pre-requisites: ##

- Visual Studio 2017 with Docker Support installed via the VS2017 installer
- Docker For Windows (https://www.docker.com/docker-windows)
- ​PartsUnlimited HOL with dotnetcore v2.0
- An active Visual Studio Team Services account
- Project Admin rights to the Visual Studio Team Services account
- An account in Azure

## Tasks Overview: ##
**1. Setting up Docker Support:** In this step you will build off of your previous modules by adding Docker support for your  PartsUnlimited source code, and they you will build,compile, and debug locally with your image

**2. Setting up Azure Container Services:** In this step you will set up Azure Container Services in VSTS for Conintuous Deployment be able to deploy to Azure.

**3. Continuous Integration:** In this step you will import Build definition template and configure it for your repository.

**4. Continuous Deployment:** In this step you will import Release definition template and configure it for your build definition.

**5. Export Build and Release Definitions from VSTS:** In this step you will export Build and Release Definitions into your local repository.

**6. Confirming successful deployment to Azure:** In this step you will confirm the status of your deployment in VSTS and Azure.


## Tasks
### Task 1: Setting up Docker
**Step 1** Follow the instructions for installing and configuring Docker For Windows here: https://docs.docker.com/get-started/

**Note** You will have to create a new docker id that is the same as your domain id. It should be in the format of your.name@domain.com. Otherwise, you will not be able to get images from https://hub.docker.com

**Note** You don't have to perform all of the steps in the Docker Get Started documentation. You just need to install Docker for Windows and create a Docker hub ID that matches your domain email address.

### Task 2: Building PartsUnlimited Locally
**Step 1**: Open the PartsUnlimited solution from your local repository. Right-click the UnlimitedPartsWebsite project and select Docker Support or click the Project Menu and select Docker Support. This will now add a new docker compose project and add DockerFile to your website.

**Step 2:** Ensure tha that you have both the Docker Compose project and the DockerFile in your PartsUnlimitedWebsite project folder.

**Step 3:** Select the Docker Compose project, right-click and make the project The Startup Project.
  [](media/StartupProject.png)
Perform Ctrl-Shift-B to build the solution.

**Step 4:**
Make sure you logged into your Docker Windows account
[](media/DockerLoggedIn.png)
Ensure Docker Compose Project is set as the startup project. You can confirm by looking at the Debug Menus:



### Task 3: Building PartsUnlimited in VSTS
**Step 1.** Navigate to the "Build & Release" tab and click on "Builds". If you already have at least one build definition then skip to the next step, otherwise we are going to create one very quickly to get an import build option.

* Click on the "+ New" button, select "DotNeCore" definition and click "Next". On the next page click "Create". This will create a DotNetCore base build definition with default settings. Click on "Save" and then "OK".

  ![](<media/4.png>)

* Click on "Build Definitions".
  ![](<media/27.png>)

**Step 3.** Click on the ellipsis (...) button next to any build and click on "Import" option.

![](<media/5.png>)

**Step 4.** Click on "Browse" and select `dotnetcore CI HOL.json` file in `templates\build definitions` of your repository. Then click on "Import" button.

![](<media/6.png>)
>**Note:** Since the template doesn't contain any information related to this project or this repository, it will take settings from the first build definition the extension finds.  That's why this extension requires at least one build definition to be there already.

**Step 5.** If you had to define an empty build definition before, it can now be deleted by clicking on the ellipsis (...) button and selecting "Delete definition".

![](<media/7.png>)

**Step 6.** Now, we need to define triggers for this imported build definition.

1.  Click on the build definition and then click on "Edit"

  ![](<media/18.png>)

2. Navigate to "Triggers" tab, check "Continuous integration (CI)" and make sure that your master branch is specified as a trigger. Click on "Save", then "OK".

  ![](<media/19.png>)

**Step 7.**  At this stage it's a good idea to check that the imported build successfully builds your project. Trigger the new build with default settings by clicking on the "Queue new build..." button and then clicking on "OK".

![](<media/8.png>)
> **Note:** The build process may take a while, but there is no need to await its completion before proceeding.

Congratulations, you have now created a build definition successfully.



### Task 4: Continuous Deployment:
**Step 1.** Navigate to the "Build & Release" tab and click on "Releases". If you already have at least one release definition then skip to the next step, otherwise we are going to create one very quickly to get an import release option.

* Click on the "+ New definition" button and select "Empty" definition and click "Next". On the next page click "Create". This will create an empty release definition with default settings. Click on "Save" and "OK".  

  ![](<media/9.png>)

**Step 2.** To import a release template click on "+" button and select "Import release definition".

![](<media/10.png>)

**Step 3.** Click on "Browse" and select `PartsUnlimited.json` file in `templates\release definitions` of your repository. Then click on "Import" button.

![](<media/11.png>)

**Step 4.** Let's specify the artifacts(output from VSTS Build) for our release definition. Navigate to the " Artifacts" tab and click on "Link to an artifact source". Set it up as shown in the screenshot below and click "Link".

![](<media/20.png>)

**Step 5.** Right after the import, you should see your tasks highlighted in red. For each tasks you may have to select your Azure subscription for the yellow field called "Azure RM Subscription".
> The name of your Azure subscription should be 'Azure For PartsUnlimited' like descripted at the beginning of this HOL.

![](<media/211.png>)

**Step 6.** The release definition's trigger is the key to the Continuous Deployment. To deploy on every successful build there has to be a trigger referencing the CI step completed above. Navigate to "Triggers" tab, tick "Continuous Deployment" and select the previously imported build definition.

![](<media/21.png>)

**Step 7.** Now we need to specify a deployment queue.

1. To do that click on the ellipsis (...) next to the "Dev" environment definition and select "Agent queue...".

  ![](<media/12.png>)

2. Select a "Hosted2017" deployment queue and click "OK".

  ![](<media/13.png>)

3. Repeat these steps for the other two environments.

**Step 8.** Our template cannot define approvers for your environments but it's a very good idea to have them for staging and production deployments.

1. Click on the ellipsis (...) next to the "Staging" environment definition and select "Assign approvers...".

  ![](<media/14.png>)

2. Set pre-deployment and post-deployment approvers. Also tick "Send an email notification to the approver whom the approval is pending on" and click "OK".

  ![](<media/15.png>)

3. Similarly, specify a pre-deployment approver for the "Production" environment. A post-deployment approver will not be required here because production is the last deployment environment in this template.

  ![](<media/16.png>)

4. The "Dev" environment creates/updates the architecture in Azure for all three environments before deploying to the "dev" slot. For this to work you need to define passwords for test and production databases.

  * Click on the ellipsis (...) button next to the "Dev" environment and click on "Configure variables...".

    ![](<media/22.png>)

  * Enter passwords for AdminPassword and AdminTestPassword variables.

    ![](<media/23.png>)
    > **Note:** `AdminPassword` is the password for the production database. `AdminTestPassword` is the password for the test database.

  * Change the values for the first fourth parameters by adding something unique like your initials at the end of the current one, for example in my case 'jstr', then click "OK".

    ![](<media/231.png>)
    > **Note:** You have to use unique values on Azure, if not you may have an deployment error because someone is already using the same values.

5. Like the previous step, you have to modify the global variales of this definition. Click on the 'Variables' section and add something unique like your initials at the end of the current one, for example in my case 'jstr', then click "OK". 
  Save the release definition by clicking on "Save" and "OK".

  ![](<media/232.png>)

Congratulations, you have created a release definition successfully.


6. If you had to define an empty release definition before, then it can be now deleted by clicking on the dropdown arrow next to the empty definition and selecting "Delete".

  ![](<media/17.png>)

Congratulations, you have created a release definition successfully.

### Task 5: Export Build and Release Definitions from VSTS
Now that you have configured build and release definitions specifically for your repository in VSTS, it's a good idea to replace the given templates with your own.

**Step 1.** Navigate to the "Build" tab. Click on the ellipsis (...) button next to the build definition you would like to export and select "Export". This will trigger a download of the build definition in JSON format. Place this file in the `templates\build definitions` directory of your local repository.
![](<media/2.png>)

**Step 2.** Navigate to the "Release" tab. Click on the dropdown arrow next to the release definition you would like to export and select "Export". This will trigger a download of the release definition in JSON format. Place this file in the `templates\release definitions` directory of your local repository.
![](<media/3.png>)

**Step 3.** Commit your changes using the following commands:

			git add .

> Stages all changes for the next commit

			git commit -m "added build and release definitions"

> Creates a commit from all current staged changes.

			git push

  > Uploads commits to the remote repository.

Congratulations, now you can reuse your templates with other projects.



### Task 6: Confirming successful deployment to Azure
The changes you have just committed will trigger a CI build and a deployment to Azure.
Once the deployment to the "dev" slot is completed, the pre-approver for the "Staging" environment will receive an email notification about the pending deployment to the "staging" slot.


Congratulations on successfully setting up Continuous Integration and Continuous Deployment with VSTS.



Next steps
----------
In this lab, you have learned how to get Docker support added to your your website project. You have been able to debug locally with Visual Studio and created a build definition in VSTS for Continuous Integration.


To learn more in-depth information about CI and CD try out these labs:

- [HOL - Parts Unlimited WebSite Continuous Integration with Visual Studio Team Services](https://github.com/Microsoft/PartsUnlimited/tree/master/docs/HOL-Continuous_Integration)
- [HOL - Continuous Deployment with Release Management in Visual Studio Team Services](https://github.com/Microsoft/PartsUnlimited/tree/master/docs/HOL-Continuous_Deployment)
