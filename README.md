

## HCL OneTest - Emerald Demo


In preparing this demo, you will learn how to setup and configure the OneTest Server and HCL Commerce Emerald store front. Pre-created API, UI and Performance tests are provided for simplicity and can be executed from the SoFy sandbox to demonstrate the key features of OneTest Server.

This demo does not cover test authoring flows which require the OneTest UI/Performance and API workbench tools, however there is nothing to stop you setting up the workspaces on your local machine in order to show the test assets. Service Virtualization is not included, but support will be added in future updates.


### Pre-requisites ###

* A Git account from which you can fork the provided test repositories. This is to ensure you have full read/write capabilities to the tests, and to avoid multi-user conflicts during your demonstrations

* Approximately 30 minutes of preparation time. The large majority of this time is waiting for the Emerald demo application to start and prepare the store front data and indexes, and running tests in advance of the demo so that there are test execution reports visible in the OneTest Server dashboard.

* [Optional] An installation of the OneTest workbench products on your own machine - SoFy does not currently support desktop applications.


## Emerald Tests

For access to the provided tests, ensure you <b>Fork</b> the following repositories into your own personal GitHub account before proceeding:

| Reposiory Type | URL |
| --- | --- |
| System Model | [https://github.com/virtualmonster/Emerald_SystemModel.git](https://github.com/virtualmonster/Emerald_SystemModel.git) |
| OneTest Data Workspace | [https://github.com/virtualmonster/Emerald_Data.git](https://github.com/virtualmonster/Emerald_Data.git) |
| OneTest API Tests | [https://github.com/virtualmonster/Emerald_APIs.git](https://github.com/virtualmonster/Emerald_APIs.git)|
| OneTest UI Tests | [https://github.com/virtualmonster/Emerald_UI.git](https://github.com/virtualmonster/Emerald_UI.git)|
| OneTest Perf Tests | [https://github.com/virtualmonster/Emerald_Perf.git](https://github.com/virtualmonster/Emerald_Perf.git)|
| Postman Collection | [https://github.com/virtualmonster/Emerald_Postman.git](https://github.com/virtualmonster/Emerald_Postman.git)|
| JMeter Tests | [https://github.com/virtualmonster/Emerald_JMeter.git](https://github.com/virtualmonster/Emerald_JMeter.git)|

> There are 2 branches corresponding to the 2 versions of HCL Commerce available. Tests in the UAT branch are confugured for Emerald 9.1.5 and in the DEV branch for 9.1.6. Running tests for the wrong version of Emerald will result in test failures as there are multiple UI and API differences across the two

Summary of the available tests:

| Type| Test Suite Name | Details |Avg Duration
| --- | --- | --- | --- |
| API**| Add To Cart|API Suite (8 tests) adding products and validating shopping cart|<1 min|
| API**| Add To Cart and Checkout|API Suite (16 tests) including checkout and payment processing |<1 min|
| User Interface*** | Register Users & Add Address |Registration of news user(s) in the Emerald application and additional of a default shipping address. Driven by Users.csv dataset or 'Users' Data Fabrication schema|~8 min|
| User Interface***| Login Add Item & Checkout |Compound Tests comprising 3 UI tests to login, add an item into the shopping basket and to complete the entire checkout and payment process. [Pre-requisite - Users must have been created first by running the Register Users test] |~4 min|
| Performance***| BenchmarkB2CFlow |VU Schedule comprising 3 transactions (11 tests) for Guest User browse, search and checkout flows|~10 min|
| JMeter| Emerald_Homepage|Simple load test, invoking Homepage, Dining, Kitchen, Bath, Living and Bedroom pages|<1 min|
| Postman| Personalized Shopping Experience |Comprising 4 API calls without test assertions including Market Spot Data, Shopper Session and Add Qualifying Items to Cart|<1 min|

>** OneTest API results database and OneTest Quality Server is not included in this demonstration environment. API tests can be executed and will appear in the results pages, but there are no detailed HTML reports available. Future versions of this demo pack will include the full API report capability.

>*** UI and Performance tests take up to 10 minutes to execute, therefore it is advisable to run these in advance of the session so that you can demonstrate the reports immediately if you have only a short timeframe for your demo.

## Preparation

Budget for around 30 minutes to complete the setup of the OneTest/Emerald solution in SoFy, and to complete the demo configuration steps below:

<details><summary>Step 1 - Setting up the Emerald/OneTest solution</summary>
<p>

In the SoFy catalog, create a solution by adding HCL Commerce and HCL OneTest Server. This demo has been tested with the following versions:

| Catalog Tile Name | Version |
| --- | --- |
| HCL Commerce | 9.1.5, 9.1.6 |
| HCL OneTest Server | 10.1.3 |


Once the solution is built, deploy it to the SoFy sandbox. After a minute or two, when the solution console is available, login and monitor the solution content tiles. 

Emerald can take between 10 and 30 minutes to become fully ready, but we can start to prepare the OneTest Server as soon as the solution console indicates it is available. You will see a tick overlaid on the OneTest Server tile, and the link to launch the OneTest Server dashboard is then available by clicking into the 'General Information' section.

When the Emerald application is ready, **take a note of the hostname** it has been given. It will look something like 'commerce-preview.sbx1234.play.hclsofy.com', where the value sbx1324 is unique to your sandbox. 

In the solution console, navigate to the to the Kubernetes Resources page and click on the pods widget to access the various resources. Select 'Services' at the top of the page and search for 'prometheus' - one service should be listed which displays the Cluster IP value and Port. **Take note of the IP/Port for later use**.

</details>

<details><summary>Step 2 - Configuring the OneTest Server demo assets</summary>
<p>

Login and prepare the Teamspace and Project
---
<p>
Once the OneTest Server dashboard is visible, you'll need to register yourself as a user by following the "Sign up" link. 

When logged in, configure the 'TeamSpace' repository under **Manage...>Configuration...>Repository** to include the provided Emerald_SystemModel.git repository from your own GitHub account. This loads a system model representing the Emerald application. For the moment, there is not much to show here, but in future releases we will make use of the model to help guide the demo and associated test artefacts.

Next, create a project named 'Emerald' where we will begin to add the test assets. 

Load the Test repositories
---
<p>
Under the Project 'Configuration' tab you'll find 'Repositories'. Ensuring you have alreday forked them to your own GIT account, add the UI, Performance, Postman and JMeter repositories at this preparation stage. You will load the API Test repository during th customer demonstration since this is somewhat interesting for prospects to see. 

Download the provided OneTest Data workspace file from the Emerald_Data.git repository and import it into the Data Fabrication (**Author...>Data Fabrication**) perspective using the "import into workspace" wizard and loading the file 'Emerald Data Fabrication Workspace.zip'. This workspace includes a "User" data dictionary that is pre-configured to generate a random firstname, lastname, email address and phone number which is used by one of our UI tests later in the demo.

Running tests in advance of the demo
---
<p>
Wherever possible, the provided tests have been configured to use static hostnames (kubernetes internal service names) which allows the tests to reach the Emerald application even though it is deployed in a new environment each time a solution is started. 

Unfortunately, UI tests require the Emerald public hostname before running since they are unable to bypass the certificate warning when presented with a self signed certificate by the internal SoFy pods. Before executing the UI Tests "Register Users" and "Login Add Item & Checkout" you must add two variables to each test; 'hostname' and 'port_num'. Add these by navigating to the 'Execution' perspective, locating the tests and opening the execute dialog. Under the 'Variables' section you can add the two variables hostname and port_num, setting the values as per your Emerald deployment. Once you execute the test for the first time, these variables become 'sticky' and persist so that you won't need to enter them again.

| Variable Name | Example Value |
| --- | --- |
|hostname | commerce-preview.sbx1234.play.hclsofy.com |
|port_num | 443 |

>Make sure to run the "Register Users" UI test in advance of "Login Add Item & Checkout" since the users must be added to the Emerald system before a full checkout process can be executed

The Postman test suite 'Personalized Shopping Experience' also suffers from execution failure when presented with a self signed certificate, but in this case we can bypass it. Adding the argument <code>--insecure</code> in the 'Program Arguments' section of the Execution dialog, under Advanced Settings solves this issue. This argument needs to be set once only and is persisted via sticky settings for subsequent execution.

The JMeter test "Emerald Homepage" requires some additional temporary JVM arguments when run in the SoFy sandbox (this will be unnecessary as of OneTest Server 10.2). When executing the "Emerald Homepage" performance test, navigate to the __Advanced__ execution configuration options and add the JVM Arguments -Xmn500m -Xms1024m -Xmx1024m before executing the test. Failure to specify these arguments can sometimes lead to the JMeter pod failing on intialization.

If you have time and want to follow the suggested demo script, make sure to run all the tests in advance of your demo so that you have something to show straight away. Additionally, schedule one or two tests for a later date to populate the Overview page 'Scheduled Runs' widget. 

Consider labelling all test executions with meaningful information that your customer can relate to, eg "HCL Launch ID 100045", "HCL Launch ID 100047", "Azure Pipeline ID 100001", "Build ID 3524", "Release 9.1.6 Sprint 3" etc..

Shortly before starting your demo, re-run the 'Register Users & Add Address' test suite. The first time it runs against your Emerald instance it will create the users, thereafter, unless you change the test data being used, it will fail because the users already exist. Make sure you have a failed execution of this test as we will refer back to it later in the demo.

</details>

<details><summary>Step 3 - Suggested short demo</summary>
<p>

Introduction
---
<p>

Start your demo by quickly introducing the Emerald store front application. Navigate through a category, locate a product and add it to your cart. Talk about generic concepts like user registration, shopping basket, back end dependencies etc and how they all need to be tested. 

Login to the OneTest Server and explain the TeamSpace concept; that a group of people within the organisation might want physical separation of licenses, users and projects/test assets etc.

Navigate to the Emerald project that you created as a part of the 'Configuring the OneTest Server demo assets' steps. Talk through the Overview page, showing the Execution Results and New or Modified charts. Talk about the breakdown of different test assets in the project and highlight that there are currently no API tests because we haven't yet added the GIT repository for those tests.

Open the Repositories tab for the project (**Manage...>Configuration...>Repositories**) and add the Emerald_APIs.git repo. Switch back to the Overview page and highlight that we now have an better representation of the different test types.

Test Execution and Result Analysis
---
<p>

Move to the execution page and view the list of available test suites - explain the difference between a test and a test suite (comprising many tests). 

Execute some of the tests and move to the Results page (**Analyze...>Results**) to illustrate some of the execution results/reports (remember API and Postman tests only take 30 seconds to run so will generate a new result very quickly). Expand the test verdict (expand the row) and highlight some of the important values which have been captured such as time/date, the user who initiated the test, the version of the test that was executed (including the GIT branch and commit ID). If your customer operates in a regulated industry, this information may be very valuable to them and may warrant further discussion about the types of user roles/authentication and project segregation present in OneTest Server.

Jira has not been integrated in this demo environment, but if you have your own Jira instance, you might consider setting up a linked application such that you can demonstrate raising a defect directly from a failed test.

Highlight the failed 'Register Users & Add Address' test suite and open the Functional Test Report to show how the test failed mid way through execution. Highlight the first failed step 'Click on Span element whose Content is Register' and open the screenshot on the right hand side of the report to show the error message "User already exists". 

Test Data
---
<p>

Talk about test data as a major challenge in testing and that we could solve this issue a number of different ways:
1. Move to the Datasets page (**Author...>Datasets..>**) and open the Users.csv file which the test is configured to use. Show how the dataset could be edited to change the user details, or how the dataset could be cloned to create a second or third instance, each with a different set of users.
2. Talk about how static data isn't always appropriate, and that in the case of performance testing you might need hundreds or thousands of unique users for each test run. Talk about data sensitivity and regulations like GDPR which mean we can no longer simply borrow data from production environments or rely on crude data obfuscation techniques. Open the Data Fabrication page (**Author...>Data Fabrication**) and open the 'User' schema. Using this simple schema, talk about how we generate fresh data on demand based on the rules applied to the data dictionary. Explain how this user data can be generated and provisioned in several different ways. Injection direct into a database, output to a file (csv, json, xml etc) or provided via our APIs and integrations

* firstname and lastname are generated by randomly selecting from a provided sample names file containing thousands of examples
* phone number is generated using a regular expression. Show the templates we provide and illustrate that the customer can add their own to the lists
* email address is generated using a function to concatenate the randomly selected firstname.lastname and appending a domain 

Go back to the Execution page and open the Execution dialog for the Register Users test suite. Select the 'Data Sources' tab and show how the configured Users.csv file could be overridden to apply a different dataset (Users2.csv), or to use the Data Fabrication schema we just looked at. Select the 'User/User' schema and configure the number of generated rows (users) required from OneTest Data. Then 'set' and execute the test. Highlight how the 'seed' value is used when the user wishes to refer to a previously used dataset. The test will now execute, and will create as many users as are specified in the Data Sources override (remember that UI tests take a while to run, so you will want to use only a couple of users if you intend to show the results during your demo)

Performance Tests and Resource Monitoring
---
<p>

Once your UI test suite is running, talk about performance testing to whatever degree your customer seems interested (low intensity/early api performance engineering and/or more traditional high intensity late stage performance testing) and point out the importance of environmental observation when analyzing test results. Move to the Resource Monitoring page (**Manage...>Resource Monitoring**) and add a new Prometheus Server monitoring source, adding the IP address and Port that you located in the SoFy solution console earlier (**Kubernetes Resources...>Services...>xxx-prometheus-server**). Select all, or a range of the default values presented by the prometheus server to capture cpu/memory/network metrics and save the configuration. 
Open the resulting resource monitor source and show how data is being collected. Give the resource monitor a label "Emerald Metrics". If your customer is interested in the resource monitoring capabilities, explain how there are many types of metrics collectors including but not limited to Prometheus. You may also wish to demonstrate adding a Prometheus Query as a counter configuration, for example:

* <code>avg(irate(container_cpu_usage_seconds_total{pod=~"comdemo.*"}[1m])) by (pod)</code> would present cpu usage for only those pods with 'comdemo' in the name, excluding the OneTest Server running pods. 
* <code>sum (container_memory_usage_bytes{pod=~"comdemo.*"}) by (pod)</code> would collect only memory utilized by the comdemo Emerald pods

>Further examples of Prometheus queries can be found by browsing the SoFy Monitoring page which presents a Grafana dashboard, pre-configured with a range of queries and charts.

Once the resource monitors are created and labelled, move back to the Execution page and execute the performance test BenchmarkB2CFlow. Open the Resource Monitoring tab and select one or more of the counters you configured before starting the test. At this point, the test will take around a minute to start running and approximately 10 minutes to complete. You may wish to show an existing report and walk through the different reports and charts, highlighting the 'Resources' chart that includes the counters we just configured in Prometheus

<p>

</details>



## Power of the API

At this point, your customer may want to go deeper into the test authoring experience, but it is important first to put what they have seen into context. Few customers will use OneTest Server soley to trigger tests manually in the dashboard - far more likely, they will use external services to trigger execution headlessly as part of a continuous test/deploy or DevOps pipeline.

OneTest Server integrates natively via plugins to several different platforms (HCL Launch, Jenkins, Azure DevOps etc) but also exposes execution APIs to allow for any toolchain integration.

Swagger documentation for the OneTest Server Execution APIs is provided and can be accessed via the SoFy 'API Directory'. 

Executing a test via APIs is a three step process:
1. Generate a new offline token from the OneTest Server dashboard which will be used to idenify the user calling the API. To create a new offline token, click the user icon in the top right hand corner of the OneTest Server dashboard and select 'Create Token'
2. Pass your 'offline token' to the authentication service and retrieve a time sensitive 'access token'. Note that this token expires after approximately 300 seconds.
3. Make a request to the jobs service to execute a given test configuration, passing the access token, offline token and details of the test suite you want to run.

<details><summary>Curl examples</summary>
<p>

## AuthToken Request:

Make the authentication request in order to retrieve your temporary access token.

<code>curl -X POST -H "Accept: application/json" -d refresh_token="__offline token from OTS__" https://onetestserverhostname/rest/tokens/ -k</code>

Sample Response:

<code>{"access_token":"__access token__","scope":"testserver email offline_access profile","session_state":"a43cac4e-9d0b-40f7-8681-76ecd6ef4d23","token_type":"bearer"}</code>

Copy the __access token__ value from the response for use in the execution request below

## Execution Request:

Using the execution request template below, replace the highlighted values as below:

*  Replace the __Bearer__ value with the access token you copied from the authentication request.
*  Replace the __offlineToken__ with your own offline token. 
*  Replace the __onetestserverhostname__ with the hostname of your solution sandbox.
*  Replace the __testAsset__ section with an appropriate configuration for your chosen test type and configurations. The easiest way to find the appropriate configuration is using the developer tools in your browser to capture the request made when executing a test from the OneTest Serer dashboard. Find the /execution? request and copy the JSON body from the request and use it to replace the one in the CURL sample given below. Typical values include:
* -------- assetId is unique to your environment and can be found using the developer tools in your browser when executing a test
* -------- tags takes the value you wish to use when labelling the test execution, use this to mimic an external tool execution ID
* -------- environment name if required by API & Postman tests only
* -------- project id is unique to your environment and can be found using the developer tools in your browser when executing a test

CURL template for execution:
<code>Curl -H "Content-Type: application/json" -H "Authorization: Bearer __access token__" -d '__testAsset__' https://onetestserverhostname/rest/projects/__projectid__/executions/</code>

Example CURL command - executing an API test:
<code>Curl -H "Content-Type: application/json" -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICI0ejk5UmdBdDd4WHkwSGFhVmtzV2pDYlJ5WlByVmVMSXdERzYyZVhNWGFZIn0.eyJleHAiOjE2MTgzMTkxMzIsImlhdCI6MTYxODMxODgzMiwianRpIjoiMTg2ZDU0NzktMGI4NC00ZjA0LWFmODMtOGVmZGI2ZGE4NjkzIiwiaXNzIjoiaHR0cHM6Ly9oY2wtb25ldGVzdC5zYngwMDQ1LnBsYXkuc29meS5kZXYvYXV0aC9yZWFsbXMvdGVzdHNlcnZlciIsImF1ZCI6WyJyZWFsbS1tYW5hZ2VtZW50IiwiYWNjb3VudCJdLCJzdWIiOiIyNzRkNTQ0ZS0wMjE3LTQ2OWYtOTI3ZS0xODg5YjkzYjkxZWUiLCJ0eXAiOiJCZWFyZXIiLCJhenAiOiJ0ZXN0c2VydmVyIiwic2Vzc2lvbl9zdGF0ZSI6ImE0M2NhYzRlLTlkMGItNDBmNy04NjgxLTc2ZWNkNmVmNGQyMyIsImFjciI6IjEiLCJhbGxvd2VkLW9yaWdpbnMiOlsiaHR0cHM6Ly9oY2wtb25ldGVzdC5zYngwMDQ1LnBsYXkuc29meS5kZXYvKiJdLCJyZWFsbV9hY2Nlc3MiOnsicm9sZXMiOlsib2ZmbGluZV9hY2Nlc3MiLCJ1bWFfYXV0aG9yaXphdGlvbiJdfSwicmVzb3VyY2VfYWNjZXNzIjp7InJlYWxtLW1hbmFnZW1lbnQiOnsicm9sZXMiOlsidmlldy1pZGVudGl0eS1wcm92aWRlcnMiLCJ2aWV3LXJlYWxtIiwibWFuYWdlLWlkZW50aXR5LXByb3ZpZGVycyIsImltcGVyc29uYXRpb24iLCJyZWFsbS1hZG1pbiIsImNyZWF0ZS1jbGllbnQiLCJtYW5hZ2UtdXNlcnMiLCJxdWVyeS1yZWFsbXMiLCJ2aWV3LWF1dGhvcml6YXRpb24iLCJxdWVyeS1jbGllbnRzIiwicXVlcnktdXNlcnMiLCJtYW5hZ2UtZXZlbnRzIiwibWFuYWdlLXJlYWxtIiwidmlldy1ldmVudHMiLCJ2aWV3LXVzZXJzIiwidmlldy1jbGllbnRzIiwibWFuYWdlLWF1dGhvcml6YXRpb24iLCJtYW5hZ2UtY2xpZW50cyIsInF1ZXJ5LWdyb3VwcyJdfSwiYWNjb3VudCI6eyJyb2xlcyI6WyJtYW5hZ2UtYWNjb3VudCIsIm1hbmFnZS1hY2NvdW50LWxpbmtzIiwidmlldy1wcm9maWxlIl19fSwic2NvcGUiOiJ0ZXN0c2VydmVyIGVtYWlsIG9mZmxpbmVfYWNjZXNzIHByb2ZpbGUiLCJlbWFpbF92ZXJpZmllZCI6ZmFsc2UsIm5hbWUiOiJTdHVhcnQgV2Fsa2VyIiwicHJlZmVycmVkX3VzZXJuYW1lIjoic3R1YXJ0IiwiZ2l2ZW5fbmFtZSI6IlN0dWFydCIsImZhbWlseV9uYW1lIjoiV2Fsa2VyIiwiZW1haWwiOiJzdHVhcnQud2Fsa2VyQGhjbC5jb20ifQ.V8QYU1CfxwAMNZfMuewXgk-nmaZFMCyHtSCTtvYcj_w5oxdM39Qg59i4dqc3-zf6u-87WW9eXZ8s5wtR1_Ae5kXQW02PvrJPKjJ9mAbHrYKElYCnCQrYot3jQQe27THG4ENTNquD7Oez9iyU9XM6fj13QSGcj09y6W9erUEDIAh7v6ALzBuuEeu2Dfr7-LIpd7WOmLD1JC-5MWLgwME7exU1bTXsWcWonuxMYptWF0IcOcVnMF2cbmnB-Y6zq_9zIaVQPtqYLXZyDFSeP0E5WovrGp4XCdkM3DTXtrjrVd0yi5F68SSQIAXvIzsH2kDyvPewapXAbk6XLC3tdrNbmg" -d '{"testAsset":{"assetId":"0211c4b0-974c-4c3a-91c4-b0974cdc3ad7","revision":"DEV","requestedVersion":null},"tags":["BuildID 000148"],"environment":"Commerce React Store PRIVATE","remoteLocations":[],"offlineToken":"eyJhbGciOiJIUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICI3NmU1ZjkwYy1kMzM1LTRmMGItYmM1NC0yYzk3YWNhMzViZGYifQ.eyJpYXQiOjE2MTgzMTA5NjcsImp0aSI6IjIwYWE2NGMzLWY0MzgtNDBjMy1iNjJiLTFlNmY4N2JkYjhiMiIsImlzcyI6Imh0dHBzOi8vaGNsLW9uZXRlc3Quc2J4MDA0NS5wbGF5LnNvZnkuZGV2L2F1dGgvcmVhbG1zL3Rlc3RzZXJ2ZXIiLCJhdWQiOiJodHRwczovL2hjbC1vbmV0ZXN0LnNieDAwNDUucGxheS5zb2Z5LmRldi9hdXRoL3JlYWxtcy90ZXN0c2VydmVyIiwic3ViIjoiMjc0ZDU0NGUtMDIxNy00NjlmLTkyN2UtMTg4OWI5M2I5MWVlIiwidHlwIjoiT2ZmbGluZSIsImF6cCI6InRlc3RzZXJ2ZXIiLCJzZXNzaW9uX3N0YXRlIjoiYTQzY2FjNGUtOWQwYi00MGY3LTg2ODEtNzZlY2Q2ZWY0ZDIzIiwic2NvcGUiOiJ0ZXN0c2VydmVyIGVtYWlsIG9mZmxpbmVfYWNjZXNzIHByb2ZpbGUifQ.a3Pkn2Fy-T2KN-u3ScAPzrln55AZeTcLKgky-_v5va0"}' https://hcl-onetest.sbx0045.play.sofy.dev/rest/projects/1250/executions/</code>

</details>


## Videos


| Part 1 - SoFy Setup| Part 2 - OneTest Setup | Part 3 - Demo | Part 4 - Execution API |
| --- | --- | --- | --- |
| <img width=100>[![Part 1 - Create the SoFy solution](https://hclcr.io/files/sofy/catalog/onetest-server-demo/generic/part-1-thumbnail_small.png)](https://youtu.be/hv83JVRgBa8)</img> |<img width=100>[![Part 2 - Configure the OneTest Server Emerald environment](https://hclcr.io/files/sofy/catalog/onetest-server-demo/generic/part-2-thumbnail_small.png)](https://youtu.be/lb5U2X906Dc) </img>| <img width=100>[![Part 3 - OneTest Server Demo](https://hclcr.io/files/sofy/catalog/onetest-server-demo/generic/part-3-thumbnail_small.png)](https://youtu.be/NzhDVzxCyas) </img>|<img width=100> [![Part 4 - Power of the API](https://hclcr.io/files/sofy/catalog/onetest-server-demo/generic/api-thumbnail-small.png)](https://youtu.be/fhCsgt82wso) </img>|

## Additional Information

<details><summary>Products Involved</summary>
<p>

| | | |
| --- | --- | ---|
| HCL Products included in this demo | HCL OneTest Server 10.1.3 or higher | HCL Commerce Emerald 9.1.6 |

</p>
</details>  

<details><summary>How It Works In SoFy</summary>
<p>

If you are not familiar with SoFy deployments, please start with the **SoFy User Guides** from the SoFy web site.

### Access URLs

Once your solution is deployed, you can find all access URLs for this demo as well as the required product(s) from the SoFy Solution Console web user interface. Depends on whether your solution is deployed in the SoFy Sandbox or in your own Kubernetes cluster, the default URL will be different. Please refer to the user guide of the required product(s) for details.

</p>
</details>
