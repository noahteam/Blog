title: JMeter from Scratch - Step by Step Tutorial
author: chen chao
tags: [JMeter, Load testing]
---


[Apache JMeter](http://jmeter.apache.org/) is a very famours open source software to perform load testing for web application. This post is to demo how to build a Jmeter load testing for a website from scratch.


# Table of Contents
1. [Preparation](#1-_Preparation)
  1.1. [System to be tested](#1-1-_System_to_be_tested)
  1.2. [Loading testing design](#1-2-_Loading_testing_design)
  1.3. [Install JMeter](#1-3-_Install_JMeter)
2. [Login with one user](#2-_Login_with_one_user)
  2.1. [Create Test Plan](#2-1-_Create_Test_Plan)
  2.2. [Create Thread Group](#2-2-_Create_Thread_Group)
  2.3. [Login](#2-3-_Login)
  2.4. [Add Results Tree](#2-4-_Add_Results_Tree)
  2.5. [Cookie Manager](#2-5-_Cookie_Manager)
3. [Login With 100 Users](#3-_Login_With_100_Users)
  3.1. [Define Variables](#3-1-_Define_Variables)
  3.2. [CSV Data Set Config](#3-2-_CSV_Data_Set_Config)
  3.3. [Change to 100 Threads](#3-3-_Change_to_100_Threads)
  3.4. [More Listeners](#3-4-_More_Listeners)
4. [Update Book](#4-_Update_Book)
  4.1. [Create HTTP Request to Update Book](#4-1-_Create_HTTP_Request_to_Update_Book)
  4.2. [Response Assertion](#4-2-_Response_Assertion)
  4.3. [Extract Data From Response](#4-3-_Extract_Data_From_Response)
5. [Logout](#5-_Logout)
6. [Conclusion](#6-_Conclusion)
  
  
  
# 1. Preparation

## 1.1. System to be tested

We have a simple system for this tutorial, referring to the following flow:
![System Overview](http://drive.google.com/uc?export=view&id=1jzHj4Ah4PzspppW2EbdLOvDDUgebP6CC9g "system overview")

User is required to login first. After succesfully login, a list of books will be shown. User is able to choose any of the book to update it.


## 1.2. Loading testing design
To do testing with this simple system, we are going to follow the flow:
1. Login
2. Update the book
3. Logout

And to add on loading, we are going to create 100 users to do above steps simultaneously.

## 1.3. Install JMeter
We need to have Java runtime environment in order to run JMeter. JMeter is downloaded from [Download Link](http://jmeter.apache.org/download_jmeter.cgi). When this tutorial is written, the latest version is 2.13. And the tutorial is using windows system. There is no much difference from operation system used.

After downloaded JMeter, there is a jmeter.bat file after un-archive. Double click jmeter.bat, the JMeter will be opened
![JMeter-empty](http://drive.google.com/uc?export=view&id=1Jm3y4dQl6rPp2GUn94h-Nj-0Fu5oKPxWzQ "jmeter empty")


# 2. Login with one user

## 2.1. Create Test Plan
Once open JMeter, we could edit the name of test plan. 
![edit test plan name](http://drive.google.com/uc?export=view&id=1zKqcq7xTqZy1AHt4Vg3m92UvSh_Kg5tuxQ "edit test plan name")
After edit the name, do not forget to save it. By default, the test plan is saved as .jmx file and you could load this .jmx file next time if you want to re-execute the testing.
![save test plan](http://drive.google.com/uc?export=view&id=1wAuOH-CphMYnQCGCm9yEe5X2SViW1fi_bg "save test plan")

## 2.2. Create Thread Group
Normally we need at least one thread group to perform load testing. To create the thread group, right click on the test plan name in right menu, then from popup menu, choose `Add`->`Threads (Users)`->`Thread Group`
![Create thread group](http://drive.google.com/uc?export=view&id=1-0pyui9nvP2loCOYur5-SLrQPjmaItArEg "create thread group")
You can rename the thread group name as your favor
![Edit thread group name](http://drive.google.com/uc?export=view&id=18XdGjgH4CetAIohwIgM8isjgXuuO4HYMSg "edit thread group name")

There are few settings on the thread group, we leave it right now and look back later.

## 2.3. Login
Now let's proceed to our first HTTP request to login to the system. 

To create a HTTP request, right click on the thread group, from the popup menu, choose `Add`->`Sampler`->`HTTP Request`
![Add Login HTTP Request](http://drive.google.com/uc?export=view&id=125S7Xybb8SkQkqsP5jh1VzwiRw_7dcgViA "Add Login HTTP Request")

We need to edit settings of this login request. In order to know the settings, normally we need to use tool to help us to identity with a trial on the system. If you are using Chrome, you could press F12 to open developer tools. Lastest version of Firefox and IE are have similar feature. We need to utilize this tool to capture the post paramters for login process. To do this, let's open our testing site.
![login page](http://drive.google.com/uc?export=view&id=1kyHMrGHAMYo8R0qEMLTQF8Zwwy3fMJkkug  "login page")
Fill in username and password, click [_Login_]. You should able to see following information in F12.
- Request URL
- Form Data

![login request capture](http://drive.google.com/uc?export=view&id=1rNLpfLzFocr302N-aZC4L5i0mJqC7jMG1A "login request capture")

After collecting the information by manully trial, we could edit the settings of Login HTTP request.
- *Name*: change it "Login".
- *Server Name or IP* : this is the domain of [_Request URL_] captured. 
- *Port Number*: if leave empty, default to 80.
- *Implementation*: you could choose one version of HttpClient.
- *Protocal*: in this tutorial, it is HTTP. Therefore, you could leave it empty.
- *Method*: in this case, it is POST
- *Path*: this is the path part of [_Request URL_] captured
- *Parameters*: you could fined them in form data captured. By clicking [_Add_] button to add in paratmers in form post. In this example, we need two paramters
  + Username : user1
  + Password : password1

We could leave other settings as default so far. The final settings should be as following
![Login HTTP Request Setting](http://drive.google.com/uc?export=view&id=1IY73YtWyzfO4g8weejyYkoed-WqKKNMryw "Login HTTP Request Setting")

Right now we already have a Http request setup. We could press [*Crtl+R*] or click [_Start_] Button on top menu to run our test.
![Start Button](http://drive.google.com/uc?export=view&id=1udJs_pi9j9f-2S4DA3BsqGVB0bPveeaFlA "Start Button")

Actually we can not see anything from running our test. In order to view some output, let's proced to next step.

## 2.4. Add Results Tree
In order for us to see the output of the excution of the test, we could add a listener to thread group. To do this, right click on thread group, from the popup menu, choose `Add`->`Listener`->`View Results Tree`
![add results tree](http://drive.google.com/uc?export=view&id=1rVVM2-d8Da8rSRf0zhF5pGMj05mquIb4Dw "add results tree")

Let's run the test again by pressing [_Crtl+R_]. Once done, clicking on [View Results Tree] in left menu, we could see there are outputs show in right panel. You may able to expand the request if there are redirects happended. In our case, once successfully login, it is supposed to go to [_Book List Page_]. However, from the screenshot below, we are seeing that it redicted to [_Book List Page_], and then redirected again to error page with message "_Invalid Session_". 
![View Results Tree 1](http://drive.google.com/uc?export=view&id=1qt1gajb03DZXxW153Zu0EjiKg-tiy-EkpQ "View Results Tree 1")

This is due to our test does not maintain the session information. Therefore, after successfully login, it fails on session checking when redirect to [_Book List Page_]. So it redicts to [_Error Page_]. We are going to resolve it in next step.

## 2.5. Cookie Manager
Most of website keeps session Id in cookie. Therefore, to keep our session valid, we just need to maintain cookie. JMemter provides [*HTTP Cookie Manager*] for this purpose. Right click thread group, in the popup menu, choose `Add`->`Config Element`->`HTTP Cookie Manager`
![Cookie Manager](http://drive.google.com/uc?export=view&id=1vTJLqHHm77WqCY2O-DpRf5KzIb7izsMRKQ "Cookie Manager")


We could drag and drop the [_HTTP Cookie Manager_] on top of [_Login_] Http request. It is my favor style but not neccessary. Now we are going to run the test again. And we will not be redirected to [_Error Page_] any more.
![View Results Tree 2](http://drive.google.com/uc?export=view&id=1ObavXN354y59_Ny8CKYKKiadNbICm60WpQ "View Results Tree 2")


# 3. Login With 100 Users

In above section, we have created a test plan for 1 user. However, load testing requires **LOAD** to the system. Therefore, we are creating load in this section.

## 3.1. Define Variables
First of all, in order to create 100 users, we have to change the *username* and *password* for login request from hardcode value to dynamic variables. In JMeter, the variable is referred by format `${variable_name}`

To define variables, we could right click on thread group, choose `Add`->`Config Element`->`User Defined Variables` in popup menu. Drag and drop it on top of [*Cookie Manager*].
![Add User Defined Variables](http://drive.google.com/uc?export=view&id=1pWHYXXQKTVtA-zk-vVvXn4VBG46PAHqOEQ "Add User Defined Variables")

In right panel of [*User Defined Variables*], click [*Add*] button in bottom. Set as following
- Name : Server Name
- Value : blog.localdev.net

![User Defined Variables Setting](http://drive.google.com/uc?export=view&id=15RPosRRVFYaVMxd59ChPOedCFCNWMUCiFA "User Defined Variables Setting")

To use the variable we just set, click on [*Login*] in left menu. Change [*Server Name or IP*] to `${Server Name}`. Then press [*Ctrl+R*], we would see it is successfully run in [*View Results Tree*]
![Login with variable](http://drive.google.com/uc?export=view&id=12KNz0VMWuN2OgBxy9KG1YgYv5GoaOJSAjw "Login with variable")

We could utilize this feature to make our test plan more reusable. However, to create 100 users, it is better to user another way to do it.

## 3.2. CSV Data Set Config

We could create a separated `d:\accounts.csv` file to store all the accounts used for load testing. 
```
user1,password1
user2,password2
user3,password3
user4,password4
user5,password5
user6,password6
user7,password7
user8,password8
user9,password9
user10,password10
user11,password11
...
...
```

In order to load this .csv file into JMeter test plan, right click on thread group, choose `Add`->`Config Element`->`CSV Data Set Config` from popup menu.
![add csv data set config](http://drive.google.com/uc?export=view&id=1zl82kxtfjGiUbmGDNy2ihRPsv8fmvRrcOg)

We need to edit settings:
- Name: `User Accounts`
- Filename: absolute/relative path to the .csv file
- Variable Names(comma-delimited): `Username,Password`

Leave other as default. The final setting will be as following:
![settings of csv data set config](http://drive.google.com/uc?export=view&id=13TPcqrYIJQ-MQJNeB19FQH-wdykpm-P3nQ)

After setup [*csv data set config*], we already have *Username* and *Password* loaded from .csv file. To use these variables in our test plan, click on [*Login*] in left menu, change the value of *Username* to `${Username}`, and value of *Password* to `${Password}`
![Login Setting](http://drive.google.com/uc?export=view&id=1k9uf02PhZpHNl6mpgL_zG7eW8M0Rp0jLTg)

Now we could press [*Ctrl+R*] to run our test again. In [*View Results Tree*], we could see there is 1 request send to login page succesfully with the 1st credential in accounts.csv file.
![Results](http://drive.google.com/uc?export=view&id=1ynq81KfhEQszMRNZfArVsrYkCPNf4EwcRQ)


## 3.3. Change to 100 Threads
Click on thread group, change [*Number of Threads (users)*] from 1 to 100. 
![100 Threads](http://drive.google.com/uc?export=view&id=13L-R3khC68TPvbPe_6Cj4hovT9RZU_YGiA)

Let's run the test again. Click on [*View Results Tree*] we could see 100 requests are sent to login. We could exam the last request. The parameter sent should be `username100` and `password100` which is the last record in `accounts.csv`
![Results Tree](http://drive.google.com/uc?export=view&id=12Epsoh8vGOGQDDZ7hLC66yzjTaalpk2TJw)

It does not matter if we have less or more credentials in accounts.csv compare to number of threads we configured. JMeter will use round-robin algorithm to pick up data from the data set. If finished last line, it will pick from the starting again.

## 3.4. More Listeners
In load testing, we may more care about server response time or throughput. There are many listeners supported in JMeter. We could add few more to test.
- Graph Results: `Add`->`Listener`->`Graph Results`
  ![Graph Results](http://drive.google.com/uc?export=view&id=11bqdjKJRQ6bfoiIUjo97Yvm5JUdLUssM1w)
- Aggregate Graph: `Add`->`Listener`->`Aggregate Graph`
  ![Aggregate Graph](http://drive.google.com/uc?export=view&id=1rKh6jv6WV3K80e5dVr-ZR54JM6nzt4wfhQ)

We also could save the test results in another file. It could be done by specify filename in any one of listeners.
![output result](http://drive.google.com/uc?export=view&id=1JANGf2yg4p0rca7WdV92YoFmFOvjvMR7vg)

The results saved will be looked like following:
```
1436468823479,82,Login,200,OK,JMeter Tutorial Thread Group 1-3,text,true,4359,28,28,12
1436468823477,107,Login,200,OK,JMeter Tutorial Thread Group 1-2,text,true,4359,36,36,14
1436468823499,150,Login,200,OK,JMeter Tutorial Thread Group 1-18,text,true,4359,81,81,66
1436468823487,129,Login,200,OK,JMeter Tutorial Thread Group 1-10,text,true,4359,64,64,57
1436468823482,81,Login,200,OK,JMeter Tutorial Thread Group 1-4,text,true,4359,31,31,12
1436468823476,70,Login,200,OK,JMeter Tutorial Thread Group 1-1,text,true,4359,25,25,9
1436468823482,184,Login,200,OK,JMeter Tutorial Thread Group 1-5,text,true,4359,87,87,78
1436468823488,164,Login,200,OK,JMeter Tutorial Thread Group 1-9,text,true,4359,83,83,56
1436468823489,365,Login,200,OK,JMeter Tutorial Thread Group 1-11,text,true,4359,180,180,95
1436468823484,168,Login,200,OK,JMeter Tutorial Thread Group 1-7,text,true,4359,83,83,78
1436468823499,172,Login,200,OK,JMeter Tutorial Thread Group 1-13,text,true,4359,89,89,62
1436468823494,219,Login,200,OK,JMeter Tutorial Thread Group 1-15,text,true,4359,142,142,94
1436468823491,181,Login,200,OK,JMeter Tutorial Thread Group 1-14,text,true,4359,89,89,72
.....
.....
```


# 4. Update Book

Right now we have already login to the system, let's proceed to next step [**Update Book**]. In order to make test simple, we temperially change the number of thread back to 1.

## 4.1. Create HTTP Request to Update Book
Similar as creating [*HTTP Request*] of [*Login*] by `Add`->`Sampler`->`HTTP Request`. Rename it to `Update Book`.

We need to do a manually trial in the system in order to get all parameters required to update a book. We capture the network traffic again.
![Update book network traffic capture](http://drive.google.com/uc?export=view&id=1gaFJUF0eEMY3CglqFFsMIhhabRXfv8mPOA)

According to the traffic captured, we configure the setting of [*Update Book*].
- Server Name or IP: `${Server Name}`
- Implementation: `HttpClient3.1`
- Method: `POST`
- Path: `/JmeterTutorial/Book/Edit/1`
- Parameters:
  + \_\_RequestVerificationToken: `PDCC7CppV-wSE51IFuPuOfKpQdo64t-8gt72qmo5zp6SYlwXTlDtOzRLLFsG3N24DHGQtAY43jeaGFeGnJsW7a_WBXgHfzx7E4Za_0PnjcQ1`
  + Id: `1` 
  + SerialNumber: `37218739821`
  + Title: `Circling the Sun`
  + Author: `Paula McLain`
  + Price: `12.90`
  
The final setting will look like following
![Setting of Update Book](http://drive.google.com/uc?export=view&id=1DSPu1Gg4fTwkRQlGFLhDYPBrNuqT0gwhIg)

we run the test again with 1 thread, we could see that [*Error Page*] is shown after requesting to [*Edit Book Page*]. The error message shows that "*Anti-forgery checking failed*".
![Results Tree](http://drive.google.com/uc?export=view&id=1la6pbA3mXvDobtRj3KUav1jwrQKhckz9LQ)

The error is because of *\_\_RequestVerificationToken* we sent is dynamically changed whenever we visit [*Edit Book Page*]. We need to extract the correct value of *\_\_RequestVerificationToken* before sending request to update. We are going to resolve it soon.

## 4.2. Response Assertion

Before we resolve the anti-forgery request parameter, we look at one more issue. Right now in [*View Results Tree*], although it fails on updating book, it shows green icon which suggests '*Success*'. It will bring trouble if we have thousands of threads since we could not exam the details for every request to know which one has error. We could do this automatically by add [*Response Assertion*].

Right click on [*Update Book*] in left menu, choose `Add`->`Assertions`->`Response Assertion` in popup menu. 
![Add Response Assertion](http://drive.google.com/uc?export=view&id=1z3oJrpbbDR6BPD1TY85nrnCURF0et1u4yw)

If we update book successfully, it should go back to book listing page. Therefore, we could validate the body of response should have `Book List` text. The setting page should be as following
![Response Assert Setting](http://drive.google.com/uc?export=view&id=1cokbEH5hHIiS9NU1UKOJwBKK4DxpZ1asyg)

Now we run the test again, we could see there is a red icon before [*Update book*] which suggests there is an error happened.
![Results Tree](http://drive.google.com/uc?export=view&id=1eOEmQ1_6SWEFMidQS9GE2dIOdFIw2fXgNQ)

Sometimes it is meaningless to continue execute the test if there is an error happened, there is a setting that could stop proceeding if error detected. Click on thread group, and select option when sample error happened. In this tutorial, we choose `Stop Thread`.
![Stop Thread When Sample Error](http://drive.google.com/uc?export=view&id=1E1k8gUqAJCWri_LmrM5vQimjWj5fRXUx3A)


## 4.3. Extract Data From Response

To resolve the anti-forgery issue, we need to visit [*Edit Book Page*] and extract *\_\_RequestVerificationToken* from the response.

### 4.3.1. Add HTTP Request To Show Edit Form

Add a [*HTTP Request*] between [*Login*] and [*Update Book*]. This request to `/JMeterTutorial/Book/Edit/1`. Name this request to `Show Edit Form`.
![show edit form](http://drive.google.com/uc?export=view&id=1wwMlivJ2mYs5nYPPl1jh5zGDmM5dhAAivw)

### 4.3.2. Extract Response

Right click on [*Show Edit Form*], choose `Add`->`Post Processors`->`Regular Expression Extractor` in popup menu. 
![Add regular expression extractor](http://drive.google.com/uc?export=view&id=1Vu_P-yeuzRDo4jtNj26r_Mj4JG0bCr4VPg)

Change the name to `Extract Anti-Forgery Token`. And edit the setting:
- Field to check: `Body`. 
  The *\_\_RequestVerificationToken* is in response body. 
- Reference Name: `token`. 
  This is the variable name for the value after extraction.
- Regular Expression: `<input name="__RequestVerificationToken" type="hidden" value="(.*)" />`.
  The regular expression to match the value. The value will be retrieved inside `()`
- Template: `$1$`
  *$1$* refers to the value inside first *\(\)*

The final setting will look like following:
![regular expression extractor setting](http://drive.google.com/uc?export=view&id=1_EvOcSgsFnIiojagBbBRPiVCDc-A6rmoSw)

### 4.3.3. Apply Token extracted to Update Book Request

We have extracted token from [*Show Edit Form*]. Click on [*Update Book*], change value of *\_\_RequestVerificationToken* to `${token}`. 
![update book setting](http://drive.google.com/uc?export=view&id=1qTdEeus2hlfHVUjR7E23jNNu4g8_sYdAdQ)

We run the test again, we should see all requests are success.
![Results Tree](http://drive.google.com/uc?export=view&id=1W6_-aDIrjL5ePAW3SzOw13SJb3ehqCzk5g)


# 5. Logout

Last step is to add logout request. 
![logout request](http://drive.google.com/uc?export=view&id=1G1WeSpqQCpGp3E7HVrQMhZDsmdGRQFNikw)

After logout, it should redirect to login form. therefore, we could add a response assertion.
![logout response assertion](http://drive.google.com/uc?export=view&id=1FqTzahYHgK0JIvGO4bdB6IUAYAZRPf__qw)

We run the test now should see following in [*Results Tree*]
![Results Tree](http://drive.google.com/uc?export=view&id=15W6JUWE3ZbFwTCBaVbWnZs3MCLausdoXfA)

Change back the number of thread to 100. We could see result in listeners.


# 6. Conclusion

By follow this tutorial, you could be able to complete the simple load testing from scratch. There are much more functions available in JMeter. This post only covers basic but useful scenarios.







































































 


































