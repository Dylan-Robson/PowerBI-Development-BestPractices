# Power BI Development CI/CD

## Table of Contents
- [Introduction](#Introduction)
- [Multi-tier Structuring](#Multi-tier-structuring)
- [Version Control](#Version-Control)
    - [Storage Options](#Storage-Approaches)
    - [Git Repo Option](#Git-Repo-Approach)
- [Difficulties with Direct Publish](#Difficulties-with-Direct-Publish)
- [Environments in Power BI](#Environments-in-Power-BI)
    - [Environment Option 1](#Environments-Example-1)
    - [Environment Option 2](#Environments-Example-2)
    - [Environments Best Practices](#Best-practices-environments)
- [CI/CD Setup and Process for PBI Reports](#CI/CD-Setup)
    - [Sample Reports and Environments](#Sample-Reports-and-environments)
    - [Power BI Reports in AD](#PBI-Reports-in-AD)
- [Azure Build Pipeline (CI) and Power BI](#AD-Build-Pipeline)
    - [Setting up CI](#Setting-up-CI)
    - [Setting up CD](#Setting-up-cd)
- [Conclusion](#Conclusion)

---

## Introduction <a name="Introduction"></a>
<p>
When it comes to development the approach of CI/CD & Versoning is common but with PowerBI development it can be a struggle to find the best practices for verson control or implementing a CI/CD based approach since well...PowerBI is a binary file type and that can make things difficult to utilize the most common development structures.
</p>

## Multi-tier Struturing <a name="Multi-tier-Structuring"></a>

<p>A multi-tier approach is something I found after doing research on "the best practices for PBi development" and find it to be one of the best ways to manage and implement a proper PBi development plan. This approach uses something known as DTAP or Development, Test, Acceptance, and Production. The DTAP setup is commonly used in most development structures and can be used when developing PowerBI content as well </P>

<p>
Continuous Integration is apart of the CI/CD process in which we automatically merge with artifacts within a repository as well as utilize the same code base as developers. The Continuous Delivery of CD part of the above approach is useful for automatically deploying the solutions to bugs, enhancements, etc. within the software (PowerBI in our case) that we are developing. This is supported in most modern repo services and is supported within Azure DevOps!
</p>

<p>
Azure DevOps allows us to integrate the CI/CD approach for development of PBi content but in order to use the "Best Practices" for developing PBi content we must store our .pbix files in a central location where DevOps is able to access the files and be deployed. We will go over storing the .pbix files in a central location as well as verson control in conjunction with Azure DevOps. Below is an image I put together showing the process for implementing my "Best Practices" for Power BI development and illustrates all the moving parts like verson control with Git, Continuous Integration, and Continuous Delivery!
</p>

![Best Practices for Developing PBi](https://i.imgur.com/U64AUuG.png)

---
## Version Control <a name="Version-Control"></a>

Version control can be difficult at best when developing PowerBI content. It falls down to multiple factors like where the backup files are stored and how do you ensure that the files will be included in a back-up or saved as as intermediate results. This can be done in various ways and be made more efficient. Depending on the size of the PBi project, you could go with storing the files on OneDrive or Sharepoint as a low-level/easy approach or make it more streamlined using Git!

### Storage Approaches <a name="Storage-Approaches"></a>

<p>With the OneDrive & Sharepoint approach you can easily implement a central storage point. Basically what this allows is you to have a central point of "truth" when it comes to creating backups for your work. During the day to day work however this approach can seem like the wrong solution due to the manual uploading and downloading of the .pbix files from the storage drive every time there is a new version of the file. Luckily the sync option in both SharePoint and OneDrive helps avoid having to manually do any of the loading and syncs the .pbix files between your locally stored version of the file and the file on the central storage point. You can read more into this approach with the link below.

[Official Microsoft Documentation](https://support.microsoft.com/en-us/office/sync-sharepoint-and-teams-files-with-your-computer-6de9ede8-5b6e-4503-80b2-6190f3354a88?ui=en-us&rs=en-us&ad=us).
</p>

### Git Repositories Approach <a name="Git-Repo-Approach"></a>
<p>
The approach of using Git Repos is a standard for Version control among most development teams as it has various ways to implement CI/CD and better management of the development cycle. That is why most teams tend to migrate towards the Git Repo approach for storing all code that is being implemented and can even be used when developing Power BI content. When using Git you will have a local copy of the code or .pbix file on your machine. Based on the local copies, you will be able to continue developing upon the code to implement solutions, enhancements, etc. After you have finished implementing some sort of fix/enhancement you can easily push the local code on your machine to the Git Repo (Shared) which is then merged. By doing so you only the changes will be pushed and saved in the online (shared) repository. This creates a new version for only the new code or changes that have been pushed.
</p>

<p>
Versioning of .pbix files is a different story. Since Power BI files are binary file types, there isn't a way of checking in only the changed code. The process of pushing changes described above using Git, identifies the .pbix file as one object which has a new version.
</p>

<p>
Because .pbix files typically include an imported Dataset or Data Model, the files can also be quiet large and with that Git is not that great at handling large files by default. No worries however as there is a solution to this which is the Git extension called Large File Storage or LFS. The extension allows better file management of large files while using Git in a separate Git repository. To keep things better performing, the main repo will only include pointers to the actual locations. For every file you save using Git the LFS extension creates a pointer that saves the binary file (.pbix file) as a BLOB or Binary Large Objects.
</p>

<p>
So far we have outlined the Version Control and Continuous Integration structuing but haven't quite touched on the Continuous Delivery structure when working with .pbix files. We will go over that in the next section but first let's recap on the current section. We discussed the different approaches to implementing a central storage point for the .pbix files in development as well as version control using either SharePoint/OneDrive (Option 1) or Git Repositories (Option 2).
</p>

---
## Difficulties with Direct Publishing from PBI Desktop <a name="Difficulties-with-Direct-Publish"></a>

<p>
When publishing using Direct Publish in Power BI Desktop we run into some common issues.

- PBI Desktop needs to be installed in all environments (Production, Development, QA)
- Without proper Version control it is impossible to track changes
- Development will need access to all data sources across every environment (IE. SQL Server Prod & Dev.)
- Development needs to manually be changed regarding all environments
</p>

---

## Environments in Power BI <a name="Environments-in-Power-BI"></a>
<p>
When it comes to development environments and so on in Power BI there is two options that directly depend on the Orgination/User.

### Example 1 <a name="Environments-Example-1"></a>
- Development Environment => Dev Azure Tenant
- UAT/QA Environment => UAT/QA Azure Tenant
- Production Envionment => Production Azure Tenant

### Example 2 <a name="Environments-Example-2"></a>
- Development Environment => Development Workspace
- UAT/QA Environment => UAT/QA Workspace
- Production Environment => Production Workspace

In both of the examples of Power BI Development Environment structures the main differences is how the orginaztion has their process for developing Power BI setup.
</p>

<p>

### Best Practices <a name="Best-practices-environments"></a>
- If using cloud based storage (ie. SharePoint/OneDrive) you will not need a Gatway but for on-premises data sources you would most likely want to use a Gateway. Another advantage to Cloud-based storage solutions is if using a Gateway your credentials will be encrypted and stored in the gateway rather than the PBI Service
- Always try to reduce hardcoding your datasources in PBI reports or in other words try to use [Parameters](https://powerbi.microsoft.com/en-us/blog/deep-dive-into-query-parameters-and-power-bi-templates/) for data sources whenever possible.
- Organize the Source Control folder structure based on requirements/client-specific/data sources.

***Remember tha updating parameters using PBI REST API does NOT support certain [data sources](https://docs.microsoft.com/en-us/rest/api/power-bi/datasets/updateparametersingroup#restrictions)***
</p>

---
## CI/CD Setup and Process for PBI Reports <a name="CI/CD-Setup"></a>
<p>
The typical idea for CI/CD in application development typically resignates with using APIs or CMDLETs. We can still take the same approach with Power BI development using:

- [PowerBI REST API](https://docs.microsoft.com/en-us/rest/api/power-bi/)
- [PowerBI cmdlet's](https://docs.microsoft.com/en-us/powershell/power-bi/overview?view=powerbi-ps)
</p>

<p>

There is a great PBI extension created by Maik van der Gaag, called:

- [PowerBI Actions](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-power-bi-actions)

This extention allows for easily managing CI/CD for Power BI Development! We'll look at how the extension helps with implementing CI/CD in Power BI development. This extension is helpful with most everything for CI/CD in development of PBI but at times there are some things that we have to apply some additional steps that are not supported by the extension.
</p>

<p>

### Sample PBI Reports and Environments <a name="Sample-Reports-and-environments"></a>
For this example we will be using two different PBI reports and Envrionments (Workspaces), below is a reference for the demonstration.

**Development Environment Workspace** : PBI_CICD

**Production Enironment Workspace** : PBI_CICD_PROD

|PBI Report Name  |  Data Source Type |  Dev Data Source | Production Data Source |
|----------|:-------------:|:-------------:|------:|
| AdventureReports.pbix |  Azure SQL Database | devenvironment.database.windows.net:devdb | prodenv.database.windows.net;proddb|
| Products.pbix |   Odata   | https://services.odata.org/V3/OData/OData.svc/	 | https://services.odata.org/V3/(S(2z5tfelmekddgffwed3sq4ve))/OData/OData.svc/
|
</p>

<p>

### Power BI Reports in Azure DevOps <a name="PBI-Reports-in-AD"></a>
The first step in the process is to setup a ```source control version``` for our PBI Reports (***.pbix files***).

- Create a new Azure Repo

.

![Repo Creation Example](https://i.imgur.com/v7sqpny.png)

- Clone the repo to your local workspace and commit your ***.pbix*** files

.

![Git Clone Example](https://i.imgur.com/Zv2nj7c.png)

- Commit the changes made to the local repo back to the remote repo (Example shown is in VSCode)

.

![VSC Commit](https://i.imgur.com/HiDLu9e.gif)

- Now that we have commited the changes push them to Azure DevOps

.

![Push Changes to AD](https://i.imgur.com/KrhzSM8.gif)

- Once you have pushed the commit from your local repo you will see it on the remote repo

.

![Remote Repo Updated](https://i.imgur.com/VwWYvIf.png)

</p>

---

## Azure Build Pipeline (CI) and Power BI <a name="AD-Build-Pipeline"></a>
<p>
With the reports in the Azure Demo Repo it's time to setup the CI/CD process.
</p>

### Setting up CI (Continuous Integration) <a name="Setting-up-CI"></a>
<p>
This step of the process is actually pretty easy, what we need to do is create 2 tasks for the Build Pipeline.

- Copy-Task
- Publish-Task

The first thing to do is create the actual **Build-Pipeline**. To this find the Pipelines icon on the sidebar in AD and click on the icon.

![Pipelines Tab](https://i.imgur.com/DGdcK3w.png)

Once you are within the Pipelines tab in AD this is where you will create the Build Pipeline. To do that 
click on the `New Pipelin` button at the top right of the page.

![New Pipeline Button](https://i.imgur.com/ZNBECLI.png)

There are two different ways to create the Build Pipeline. We can either create it with the classic editor or use a YAML file. 

`Classic Editor Example`

![Classic Editor Example](https://i.imgur.com/AD4ZeiU.png)

`YAML Editor Example`

![YAML Editor Example](https://i.imgur.com/MD9SwF9.png)

The choice is yours on how you want to build your pipeline but some things to make sure you take into account are the following:

- The `trigger` in either options should be the repo that is your "Master" repo.
- Where it states the `pool` this is referring to something known as an Agent, make sure you select the one that you have setup for Automation in AD. Follow this [Guide](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/agents?view=azure-devops&tabs=browser) from Microsoft to learn more about Agent's and their setup.
</p>

### Setting up CD (Continuous Deployment)<a name="Setting-up-cd"></a>
<p>
This release pipeline in the demo is exactly that, just a demo example of how to setup the CD step in the CI/CD process. Actual release pipelines may vary depending upon the various use-cases based on the end user who is using/setting it up.

**Authentication for Power BI**

Setting up Authentication for Power BI can be done in 2 ways:

- Master Account option: This basically is using the Username and Password authentication, along with needing to register an application in AAD to access Power BI's API's.
- SPN (Service Principal) option: This option requires registering the application (PBI Application) in AAD and add your app in the PowerBI Portal.

You can read more about this with Microsoft's Documentation [here](https://docs.microsoft.com/en-us/power-bi/developer/embedded/embed-service-principal#get-started-with-a-service-principal).

There are quite a bit of differences between the options and an image provided below displays the differences amongst the two.

![Master Account vs SPN](https://social.technet.microsoft.com/wiki/cfs-filesystemfile.ashx/__key/communityserver-wikis-components-files/00-00-00-00-05/02021.7.PNG)
</p>

**Using Power BI Action Extension (Master Account Authentication)**
<p>

When setting up the Coninuous Deployment or CD step we first need to install an extension to Azure DevOps. This extension allows for us to integrate a CD approach to developing in Power BI. The link to the extension can be found and downloaded [here](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-power-bi-actions).

This extension allows for the following:
- Create Workspaces
- Delete Workspaces
- Add new Admin Users to Workspaces in Power BI
- Update DataSource Connections
- Upload Power BI Reports
</p>

<p>
However for this demo of implementing CD in Power BI Development we are going to be creating 2 Environments

- Development Environment
- Production Environment

We will have to navigate back to the Pipelines tab in AD but this time well want to create a release pipeline.

![Release Example](https://i.imgur.com/gR0LKqb.png)

Once you have migrated to the release section of the Pipelines tab click on where it says new Pipeline and lets get started.
</p>

<p>
Once you are in the Release Pipeline section of AD you'll first want to add an artifact (the one we created with CI setup). Which is the Build Artifact with what ever name you selected for your Project

![Select the Artifact](https://i.imgur.com/r114PUN.png)

Once you have selected the correct `Build Artifact` click on the Add button.
</p>

<p>

Now we have to setup our two stages `Development` and `Production` for our release pipeline. To do that we need to add them as stages in our release pipeline.

![Adding Stages](https://i.imgur.com/nGc8xuk.png)

Give the Stage a name other than the default name and change it to Development as this is the first Job (Stage) of the Release Pipeline we will be setting up.

![Renaming Stage](https://i.imgur.com/EwjwgX1.png)

You can close out of the Properties tab now as it will save the name you entered automatically and now we have to add the tasks we want this stage of the pipeline to achieve.
</p>

<p>

Add a task to this Stage by clicking on the tab that says Tasks.

![Adding Tasks](https://i.imgur.com/Afr5Cp0.png)

Just like with our other pipeline click the **+** button under where it says `Agent job` and search for **Power BI Actions**

![Power BI Actions Search](https://i.imgur.com/ctr7yfL.png)

Add that as a task for the Pipeline and now we have to setup the credentials for the task. You can add the credentials to your Power BI Service by clicking the New button and add them within there.

![Add PBI Credentials](https://i.imgur.com/iYdsSPS.png)

You can choose to either use a Username/Password option or the SCN option. Choose whichever you feel fits your needs and then click save to save the credentials you have entered.

Now in the next sections of the PBI Actions task add the Workspace name and then select the appropriate Source file (The pbix file in your Master or Main Branch).

![Workspace and Source File Example](https://i.imgur.com/WTePTmC.png)
</p>

<p>

We need to add two more tasks to this stage. One for the Odata DataSource and one for the SQL Server DataSource.

Click the Plus button again search for the Power BI extension and add the Power BI Actions Task again and name them according to the Report you're deploying

You will have to repeat this process twice since we have two datasources.

![Power BI Deploy example](https://i.imgur.com/SRSlmkm.png)
</p>

<p>

Now we have the Development Stage completed we have to do the same for the Production Stage of the release pipeline. It is nearly identical to what we just added as tasks but the only addition to the tasks for the production stage is adding the change to the data sources.

We will create a new stage in the pipeline and call it production or PROD (Or whatever name you would like) and begin adding the tasks we did for the Development stage. Once you have added all the previous tasks lets add the data source updates task.

First well create the Update tasks for the SQL Data Source. Change the name of the tasks to whatever you would like but try to keep it aligned to what the task is doing. Choose the PBI connection you setup earlier and then enter in a new workspace name for the Production environment. The image below shows exactly what you need to enter for the demo but again when it comes to an actual Power BI development project there could be differences to what you might need to enter for Updating certain data sources. The documentation found on Microsofts website provides more insight to this found [here](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/?view=azure-devops).

![SQL Update Example](https://i.imgur.com/8phRVdu.png)

We do the same for the OData data source but it has different parameters for updating this data source.

![Odata Update Example](https://i.imgur.com/6djBFvB.png)

With these tasks now added for the production stage we can Deploy the Production Reports as well as the updates to the data sources however there are some limitaions to the extension we used.

**Limitations**

- Parameter Updates
- Dataset Takeover
- Update Credentials for the Datasource(s)
</p>

**Using PowerShell Scripting in AD (SPN Setup)**
<p>

In the above setup we used the Master Account Authentication approach to setup our CI/CD environment. We will now go over the SPN (Service Principal) setup process. With this approach we can use Powershell scripting within AD and create all the release pipelines tasks as we did before but alleviate any of the restrictions or limitations that we run into when using the PBI Extension.
</p>

<p>

We will be using a self-hosted agent for this setup and again there is a provided link in the documentation to how to setup a self-hosted agent. Alternatively you could use a cloud hosted agent within AD but that will require a subscription plan. We will be creating a release pipeline similar to we previously did so again migrate to the pipelines release tab and create a new release pipeline and select the PBI artifact that we used.

To start off the tasks within our SPN Release Pipeline we will create a tasks that installs the PBI Modules we need.

![PS Script Install Module](https://i.imgur.com/jD9pIS2.png)

Next we add the Take Over data set script which allows us to actually achieve this function using PBIs REST API that the Extensions Actions tasks did not.

![PS Script TakeOver Module](https://i.imgur.com/t0JJPuY.png)

Next add another PS script tasks that updates the datasources

![PS Script Update datasources](https://i.imgur.com/fwGO3Kj.png)

Finally we add our last PS script that allows for us to update Power BI parameters which we could not achieve when using the Power BI extension in the previous section.

![PS Script Update Parameters](https://i.imgur.com/VEyTxuZ.png)


Now that we have setup all the Powershell scripts we have to set some Azure variables for the tasks in the release pipeline. Click on the Variables tab at the top of the page.

![Variable Tab](https://i.imgur.com/yNnP24U.png)

Within the Pipelines variables tab add the following variables:
- client_id
- client_secret
- credentialpassword
- tenant_id

These can all be found when you registered your PBI app or within your Azure Portal within your Azure Active Directory. If you haven't done that yet please head over to this [site](https://docs.microsoft.com/en-us/power-bi/developer/embedded/register-app?tabs=customers%2CAzure) and register the PBI app. 

***You will be required to have a Pro licenses to register your PBI app but you can get a free 45-day trial to start off.***

![Credentials as variables in AD](https://i.imgur.com/fRbPkUa.png)
</p>

---

## Conclusion <a name="Conclusion"></a>
<p>
This documentation and demo should allow you to get started with implementing a proper development environment when you or your organization is building Power BI Dashboards, Reports, etc. Power BI is always being updated and things might change from when the documentation was created but there will most likely be a solution if something might become outdated in this documentation and you can always check out more on developing environments when working with Power BI with the documentation [here](https://docs.microsoft.com/en-us/power-bi/create-reports/deployment-pipelines-best-practices) as Microsoft always provides solutions to CI/CD pipelines for Power BI within the provided link.

Happy Developing!
</p>