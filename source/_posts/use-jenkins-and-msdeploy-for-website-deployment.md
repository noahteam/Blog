title: Use Jenkins and MsDeploy for website continuous delivery and deployment
Author: Ji Zhoubo
date: 2015-06-29 21:45:15
tags: [Jenkins, msdeploy, CI]
---
## About
Normally for software development, we will have at least 2 environments: local development and production environment.  It will be a tough task for developer or server administrators to do manual release if there are too many projects or the release is too frequent.

In this post, I will show how to config Jenkins and set up MsDeploy to do continuous delivery and automatic website deployment .

## Prerequiste
This post will assume you have already set up your Jenkins, you are using TFS for source control (of course it is just small modification if you are using Git or SVN), and you are using IIS (version >= 7.0) as your web server.

## Steps
- **Set up Jenkins job for pull latest and build**
 This step is similar as what we did for other projects which are running on our Jenkins for continuous integration and testing.
 
 Use Jenkins TFS plug-in to get latest periodically

 ![alt text](https://lh3.googleusercontent.com/GLDdbnZI1dwKKa69vwSFJrteO6pBnVbtWEXmM2usz3wGPFskSCPZ6SLq9vQUD5USK_VLXQf6_prr6UM=w1566-h620)
 
 Use Jenkins MSBuild plug-in or use powershell to build solution

 ![alt text](https://lh6.googleusercontent.com/NZaaq3_pH8o6L75S_-0Rgw5wPvGpj4NkULMitpxBI_bU_P4WQqUl9PoPC1wOX3Kwy5ThSAFx5En-R5A=w1566-h620)


- **Set up msdeploy**
 1. [Download](http://www.iis.net/downloads/microsoft/web-deploy) and install msdeploy on your both source and target server
 2. Ensure **Web Management Service** and **Web Deployment Agent Service** are running on your target server, they are necessary for remote deployment


- **Prepare domain administrator account**
 To use msdeploy to deploy website to target server's IIS root path, the account must be a **domain administrator account**.
 Sounds weird right? Since it only needs to be that server's admin to have full permission for folders. Someone believe this is a bug.


- **Powershell to run msdeploy**
 ```ps1
 msdeploy.exe -allowUntrusted=true -verb:sync -source:contentpath='D:\WS\ExampleProject' -dest:contentpath=F:\webfolder,computerName=exampleproject.example.com,Username='yourdomain\username',Password='password' -skip:objectName=dirPath,absolutePath="config" -skip:objectName=filePath,absolutePath="web.config"
 ```

 - **-verb:sync**, execute operation which synchronizes data between source and destination
 - **source:contentpath**, specifies we are using contentpath as provider, and will sync everything from that directory to target
 - **dest:contentpath**, specifies the folder that content will be synced to
 - **computerName=exampleproject.example.com,Username='yourdomain\username',Password='password'**, the target server with IIS
 - **skip**, to ignore folders or files, e.g. we don't want local config files by synced to production environment, which may break the website


 We have set up another job on Jenkins to auto deploy website to production environment

 When run the script, will get output like this:
 ```ps1
 Info: Updating file (F:\exampleproject\web_root\menu\Main.aspx).
 Info: Updating file (F:\exampleproject\web_root\menu\Main.aspx.cs).
 Info: Updating file (F:\exampleproject\web_root\menu\Menu.aspx).
 Info: Updating file (F:\exampleproject\web_root\menu\M

 http://santexgroup.com/blog/continuous-delivery-using-asp-net-mvc4-web-api/

 http://blog.winhost.com/using-msdeploy-to-publish-your-site/enu.aspx.cs).
 Info: Updating file (F:\exampleproject\web_root\public\access-forbidden.html).
 Total changes: 259 (0 added, 0 deleted, 259 updated, 0 parameters changed, 21623463 bytes copied)
 ```


## Trouble Shooting
During the set up, I have met some issues, and I found this page is very useful for understanding those issues and trouble shooting
> For certain common error cases, Web Deploy will show an error code. This table explains why the error occurs and steps the user can take to avoid the error. Note that the error message may be different depending on how Web Deploy is invoked e.g. Microsoft WebMatrix chooses to show custom error messages. The error messages listed below show up on the msdeploy.exe command line and API:
http://www.iis.net/learn/publish/troubleshooting-web-deploy/web-deploy-error-codes


## More
If you have interest to know more about msdeploy and its advanced usage, below are some good references:

 https://technet.microsoft.com/en-us/library/dd568996(v=ws.10).aspx

 http://santexgroup.com/blog/continuous-delivery-using-asp-net-mvc4-web-api/

 http://blog.winhost.com/using-msdeploy-to-publish-your-site/
