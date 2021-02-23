# Deploying a Website with Azure App Services and Azure DevOps
Deploying a Website with Azure App Services and Azure DevOps
 This post provides an introductory example of how Azure App Services and Azure DevOps can be utilized for a website deployment.  This site was deployed using these methods.

 Create an Azure account https://azure.microsoft.com/en-us/free/ and login to Azure portal.  You can use an email account you already have or create a free windows live account and transition to your preferred domain later. 
 
![alt text](/posts/images/P0001/P0001_IMG01.png)

Search for App Services and select the option in the dropdown list.  Select add to create a new App Service.

![alt text](/posts/images/P0001/P0001_IMG03.png)

Next configure your App Service with the same settings below.  You may have to create a resource group if you don't already have one in place.

![alt text](/posts/images/P0001/P0001_IMG04.png)

Once you have configured your settings select the options to review and create.  The D1 option is required if you plan to use your preferred domain.

While you are waiting for this to deploy you can begin working to install Hugo.  First you will need to install Chocolatey by running the command below:
```Powershell
Start-Process "Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))"
```
![alt text](/posts/images/P0001/P0001_IMG06.png)

Next you will want to upgrade Chocolately by running the command:
```Powershell
choco upgrade chocolatey
```
![alt text](/posts/images/P0001/P0001_IMG07.png)

Now that Chocolately is installed we can use it to manage the hugo package.  First we run the command to install it:
```Powershell
choco install hugo -confirm
```
Path to the location you would like to organize your sites and create a hugosite directory:
```Powershell
mkdir hugosite
```
Move to that new directory:
```Powershell
cd hugosite
```
And then create a new site and the initial folder structure:
```Powershell
hugo new site qwarvio
```
Lets take a look at the folder structure:

![alt text](/posts/images/P0001/P0001_IMG11.png)

Next we can start the web server using the command:
```Powershell
hugo server
```
(keep this window open in the background)

![alt text](/posts/images/P0001/P0001_IMG20.png)

Open a new Powershell window and initiate the git repository under the site folder.  If you do not have git installed, you can download and install it from here https://git-scm.com/downloads.
```Powershell
git init
```
![alt text](/posts/images/P0001/P0001_IMG19.png)

Visit Hugo's themes web page https://themes.gohugo.io/ and click a theme you want to use.  On the theme's information page you will see a Download button that will take you to the github page for the theme.  Once on the github page you will want to click the 'Clone or download' button and copy the 'Clone with HTTPS' option and add it to the end of the command below after you change your directory to the themes folder:

![alt text](/posts/images/P0001/P0001_IMG14.png)

![alt text](/posts/images/P0001/P0001_IMG15.png)
```Powershell
git submodule add *your selected theme https link here*
```
![alt text](/posts/images/P0001/P0001_IMG17.png)

Stop (Press Ctrl+C to stop) and start your hugo server Powershell instance again:
```Powershell
hugo server
```
![alt text](/posts/images/P0001/P0001_IMG20.png)

Open a web browser and you should see the template displayed at the domain localhost:1313

![alt text](/posts/images/P0001/P0001_IMG21.png)

Next we need to create an Azure DevOps account or link one of your current Microsoft or Github accounts by visiting https://azure.microsoft.com/en-us/services/devops 

Once logged into the account you can select the button '+ New project' to create your new project.  You may need to create an organization or new organization to tie your project to prior to creating the project.

![alt text](/posts/images/P0001/P0001_IMG24.png)

If your organization was automatically generated and you want to change the name or some of the other settings you can click on the organization settings (I altered my organizations name to qwarvio).  I would recommend closing out of the browser and logging back in if you change the organization name to reset any cached url information. 

![alt text](/posts/images/P0001/P0001_IMG26.png)

Once you navigate back to the project Overview page click on the Repos > Files menu options and obtain the web address you will need to push your existing Hugo site repository to Azure DevOps.  Open a Powershell window on your PC, path to the site folder and run the command below inside the shell. If you are not already authenticated you may be prompted to login with your AzureDevOps login.

```Powershell
git init .
git add *
git commit -m "first commit"
git remote add origin *your listed https link here*
git push origin master
```
For subsequent changes:
```Powershell
git status
git add *
git commit -m "second commit"
git push -u origin --all
git status
```
*If these initial commits fail we will fix it later after a public folder is created.*

After you have processed your initial commit from your local site folder to Azure DevOps you need to add a few tasks to the Pipelines.  Click on the Pipelines > Pipelines and click on the commit job you just ran and then click on Edit.

![alt text](/posts/images/P0001/P0001_IMG32.png)

![alt text](/posts/images/P0001/P0001_IMG33.png)

Use the + symbol next to your 'Agent job' header to add the required tasks 'Hugo generate' and 'Azure App Service Deploy: your app name' with the settings provided in the images below.  These tasks will assist with running jobs prior to publishing the new version of your site.

![alt text](/posts/images/P0001/P0001_IMG34.png)

![alt text](/posts/images/P0001/P0001_IMG35.png)

Go back to the Azure portal and App Service you created earlier (you can search for it under all resources) and click on the Deployment Center menu and make the selections:

Source Control - Azure Repos
Build Provider - Azure Pipelines
Configure - follow my image and make changes based on your project specific settings 
Summary - Finish

![alt text](/posts/images/P0001/P0001_IMG45.png)

![alt text](/posts/images/P0001/P0001_IMG47.png)

![alt text](/posts/images/P0001/P0001_IMG48.png)

Your deployment may take a few minutes to complete but once it is live you will see the messages below.

![alt text](/posts/images/P0001/P0001_IMG52.png)

If your Hugo project does not have a public folder yet you will need to create one.  This folder will contain the structure that will be uploaded to the Azure Web App.  This can be done by running the command below while in your site folder.  You should notice that the number of site files has increased:

```Powershell
hugo
```

![alt text](/posts/images/P0001/P0001_IMG55.png)

Now push another commit to go live with these changes:

```Powershell
git status 
git add public
git status
git commit -m 'adding public folder'
git push
```

Visit your hosted site by typing your-app-name.azurewebsites.net.  

Now that we have a live app lets set it up with a custom domain.  Open your App Service settings again in the Azure portal and click on the Setting 'Custom domains'.  Click on the option 'Add custom domain', enter the desired domain that you own and validate:

![alt text](/posts/images/P0001/P0001_IMG63.png)

After clicking validate you should receive a prompt similar to the one below which will explain the TXT and A record you will need to create under your domain's DNS management.  These settings are typically found on the same site you registered your domain but can be redirected using nameservers, steps we will perform below.  If you want to test without an SSL certificate you can add these records to your current registrar (or nameservers) and then click the validate button again after the records propagate (use this site to test https://digwebinterface.com/) and your web app should be live.

![alt text](/posts/images/P0001/P0001_IMG70.png)

We will be redirecting our nameservers to Cloudflare in order to provides SSL certificate services.  This can also be accomplished through Azure setting by purchasing a 3rd party certificate and bumping up to a B1 app service plan from a D1 plan (required for SSL) but this would increase the cost dramatically.   

Navigate to cloudflare.com and create an account.  Once you have created an account you will need to add your domain to the management portal.  When setting up the site you will be provided with an option to import current DNS records - recommended and to select a plan - Free $0.  The site will provide nameserver accounts that need to be removed and added to direct DNS requests to Cloudflare going forward, see below: 

![alt text](/posts/images/P0001/P0001_IMG82.png)

Login to your registrar and make the necessary changes to the nameservers.  Reference your registrar's documentation if you need to know how to make this change.  Again as mentioned above you will now need to validate your site through Azure where we left off.  Remember that this can take some time and a good site for testing propagation is https://digwebinterface.com/.

That is it! Congratulations on deploying your first App Service in Azure utilizing DevOps and Hugo for a Static site.
