
# HCL OneTest - Emerald Demo

## **Overview**

In this demo, you will learn how to setup and configure the OneTest Server and HCL Commerce demo using the Emerald store front. Pre created API, UI and Performance tests are provided for simplicity and can be executed in the SoFy sandbox to demonstrate the key features of OneTest Server. 

This demo does not cover test authoring flows which require the OneTest UI/Performance and API workbench tools, however there is nothing to stop you setting up the workspaces on your local machine in order to show the test assets. Service Virtualization support will be added in future updates

<details><summary>Pre-requisites</summary>

* A GitHub account where you can fork the provided test repositories. 

* Optionally, you will have the OneTest workbench products installed on your machine

* Approximately 20 to 30 minutes of preparation time is needed before you'll be ready to demo. The large majority of this time is waiting for the Emerald demo application to start and prepare the store front data and indexes. 

* If you wish to show existing test reports as a part of the demo, you will need to budget for around 10 minutes of test execution time in order to run tests and generate reports.

</details>

<details><summary>Setting up the Emerald/OneTest solution</summary>

In the SoFy catalog, create a solution by adding HCL Commerce and HCL OneTest Server. This demo has been tested with the following versions:

| Catalog Tile Name| Version|
| --- | --- |
| HCL Commerce | 9.1.5.0 |
| HCL OneTest Server | 10.1.3 |


Once the solution is built, deploy it to the SoFy sandbox. After a minute or two, when the solution console is available, login and monitor the solution content tiles. 

Emerald can take 20 to 30 minutes to become fully ready, but we can start to prepare the OneTest Server as soon as the solution console indicates it is available. You will see a tick overlaid on the OneTest Server tile, and the link to launch the OneTest Server dashboard is then available by clicking into the 'General Information' section.

When the Emerald application is ready, **take a note of the hostname** it has been given. It will look something like 'commerce-preview.sbx1324.play.hclsofy.com', where the value sbx1324 is unique to your sandbox. 

In the solution console, navigate to the to the Kubernetes Resources page and click on the pods widget to access the various resources. Select 'Services' at the top of the page and search for 'prometheus' - one service should be listed which displays the Cluster IP value and Port. **Take note of the IP/Port for later use**.

</details>

<details><summary>Configuring the OneTest Server demo assets</summary>

Once the OneTest Server dashboard is visible, you'll need to register yourself as a user by following the "Sign up" link.

Once logged in, configure the 'TeamSpace' repository under **Manage...>Configuration...>Repository** to include the provided Emerald_SystemModel.git repository from your own GitHub account. This loads a system model representing the Emerald application. For the moment, there is not much to show here, but in future releases we will make use of the model to help guide the demo and associated test artefacts.

Next, create a project named 'Emerald' where we will begin to add the test assets. Under the Project 'Configuration' tab you'll find 'Repositories'. Add the bulk of the provided repositories at this preparation stage, leaving at least one that can be loaded as a part of your demo later since this is somewhat interesting for prospects to see. [Suggestion, leave the API Test repository until the live demo since these tests can be executed quickly]

Download the zip file from the Emerald_Data.git repository and import it into the Data Fabrication (**Author...>Data Fabrication**) perspective using the "import into workspace" wizard and loading the file Emerald Data Fabrication Workspace.zip. This workspace includes a "User" data dictionary that is pre-configured to generate a random firstname, lastname, email address and phone number which is used by one of our UI tests later in the demo.

All of the pre-prepared tests have been configured to use static hostnames (kubernetes internal service names) where possible. This means that you don't need to edit the tests before running them, even though your environment changes each time you start a SoFy solution. However, UI tests are a little different and require the (dynamic) Emerald public hostname. In future, the UI tests will also use static internal service names, but at the moment this cannot be achieved due to the use of self signed certificates for all services behind the ingress controller in SoFy. To mitigate this, and before executing the UI Tests "Register Users" and "Login Add Item & Checkout" you must add two variables to each test; 'hostname' and 'port_num'. Add these by navigating to the 'Execution' perspective, locating the tests and opening the execute dialog. Under the 'Variables' section you can add the two variables hostname and port_num, setting the values as per your Emerald deployment. Once you execute the test for the first time, these variables become 'sticky' and persist so that you won't need to enter them again.

| Variable Name | Value |
| --- | --- |
|hostname |  Emerald public hostname |
|port_num | 443 |

The Postman test suite 'Personalized Shopping Experience' also suffers from execution failure when presented with a SoFy self signed certificate. Adding the argument <code>--insecure</code> in the 'Program Arguments' section of the Execution dialog, under Advanced Settings solves this issue. This argument needs to be set once only and is persisted via sticky settings for subsequent execution.

If you have time, make sure to run the tests in advance of your demo and schedule one or two for a later date to populate the Overview page 'Scheduled Runs' widget. Consider labelling the test executions with meaningful information that your customer can relate to, eg "HCL Launch ID 100045", "HCL Launch ID 100047", "Azure Pipeline ID 100001", "Build ID 3524", "1.7.3 Sprint 3" etc..

>Make sure to run the "Register Users" UI test in advance of "Login Add Item & Checkout" since the users must be added to the Emerald system before a full checkout process is executed

</details>

<details><summary>Test Repositories</summary>

<p>Fork the following repositories to your personal GitHub account:</p>

Teamspace System Model
* [https://github.com/virtualmonster/Emerald_SystemModel.git](https://github.com/virtualmonster/Emerald_SystemModel.git)

OneTest Test Repositories
* [https://github.com/virtualmonster/Emerald_UI.git](https://github.com/virtualmonster/Emerald_UI.git)

* [https://github.com/virtualmonster/Emerald_Perf.git](https://github.com/virtualmonster/Emerald_Perf.git)

* [https://github.com/virtualmonster/Emerald_APIs.git](https://github.com/virtualmonster/Emerald_APIs.git)

OneTest Data Workspace
* [https://github.com/virtualmonster/Emerald_Data.git](https://github.com/virtualmonster/Emerald_Data.git)

Opensource Test Repositories
* [https://github.com/virtualmonster/Emerald_Postman.git](https://github.com/virtualmonster/Emerald_Postman.git)

* https://github.com/virtualmonster/Emerald_JMeter.git

</details>

## **The Tests**
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

## **Suggested Demo (short version)**

Shortly before starting your demo, run the 'Register Users & Add Address' test suite. The first time it runs against your Emerald instance it will create users, thereafter, unless you change the test data being used, it will fail because the users already exist. Make sure you have a failed execution of this test as we will refer back to it later in the demo.

Start your demo by quickly introducing the Emerald store front application. Navigate through a category, locate a product and add it to your cart. Talk about generic concepts like user registration, shopping basket, back end dependencies etc and how they all need to be tested. 

Login to the OneTest Server and explain the TeamSpace concept; that a group of people within the organisation might want physical separation of licenses, users and projects/test assets etc.

Navigate to the Emerald project that you created as a part of the 'Configuring the OneTest Server demo assets' steps. Talk through the Overview page, showing the Execution Results and New or Modified charts. Talk about the breakdown of different test assets in the project and highlight that there are currently no API tests because we haven't yet added the GIT repository for those tests.

Open the Repositories tab for the project (**Manage...>Configuration...>Repositories**) and add the Emerald_APIs.git repo. Switch back to the Overview page and highlight that we now have an better representation of the different test types.

Move to the execution page and view the list of available test suites - explain the difference between a test and a test suite (comprising many tests). 

Execute some of the tests and move to the Results page (**Analyze...>Results**) to illustrate some of the execution results/reports (remember API and Postman tests only take 30 seconds to run so will generate a new result very quickly). Expand the test verdict (expand the row) and highlight some of the important values which have been captured such as time/date, the user who initiated the test, the version of the test that was executed (including the GIT branch and commit ID). If your customer operates in a regulated industry, this information may be very valuable to them and may warrant further discussion about the types of user roles/authentication and project segregation present in OneTest Server.

Jira has not been integrated in this demo environment, but if you have your own Jira instance, you might consider setting up a linked application such that you can demonstrate raising a defect directly from a failed test.

Highlight the failed 'Register Users & Add Address' test suite and open the Functional Test Report to show how the test failed mid way through execution. Highlight the first failed step 'Click on Span element whose Content is Register' and open the screenshot on the right hand side of the report to show the error message "User already exists". Talk about test data as a major challenge in testing and that we could solve this issue a number of different ways
1. Move to the Datasets page (**Author...>Datasets..>**) and open the Users.csv file which the test is configured to use. Show how the dataset could be edited to change the user details, or how the dataset could be cloned to create a second or third instance, each with a different set of users.
2. Talk about how static data isn't always appropriate, and that in the case of performance testing you might need hundreds or thousands of unique users for each test run. Talk about data sensitivity and regulations like GDPR which mean we can no longer simply borrow data from production environments or rely on crude data obfuscation techniques. Open the Data Fabrication page (**Author...>Data Fabrication**) and open the 'User' schema. Using this simple schema, talk about how we generate fresh data on demand based on the rules applied to the data dictionary. Explain how this user data can be generated and provisioned in several different ways. Injection direct into a database, output to a file (csv, json, xml etc) or provided via our APIs and integrations

* firstname and lastname are generated by randomly selecting from a provided sample names file containing thousands of examples
* phone number is generated using a regular expression. Show the templates we provide and illustrate that the customer can add their own to the lists
* email address is generated using a function to concatenate the randomly selected firstname.lastname and appending a domain 

Go back to the Execution page and open the Execution dialog for the Register Users test suite. Select the 'Data Sources' tab and show how the configured Users.csv file could be overridden to apply a different dataset (Users2.csv), or to use the Data Fabrication schema we just looked at. Select the 'User/User' schema and configure the number of generated rows (users) required from OneTest Data. Then 'set' and execute the test. Highlight how the 'seed' value is used when the user wishes to refer to a previously used dataset. The test will now execute, and will create as many users as are specified in the Data Sources override (remember that UI tests take a while to run, so you will want to use only a couple of users if you intend to show the results during your demo)

Once your UI test suite is running, talk about performance testing to whatever degree your customer seems interested (low intensity/early api performance engineering and/or more traditional high intensity late stage performance testing) and point out the importance of environmental data when analyzing test results. Move to the Resource Monitoring page (**Manage...>Resource Monitoring**) and add a new Prometheus Server monitoring source, adding the IP address and Port that you located in the SoFy solution console earlier (**Kubernetes Resources...>Services...>xxx-prometheus-server**). Select all, or a range of the default values presented by the prometheus server to capture cpu/memory/network metrics and save the configuration. Open the resulting resource monitor source and show how data is being collected. Give the resource monitor a label "Emerald Metrics". If your customer is interested in the resource monitoring capabilities, explain how there are many types of metrics collectors including but not limited to Prometheus. You may also wish to demonstrate adding a Prometheus Query as a counter configuration, for example:
<code>avg(irate(container_cpu_usage_seconds_total{pod=~"comdemo.*"}[1m])) by (pod)</code> would present cpu usage for only those pods with 'comdemo' in the name, excluding the OneTest Server running pods. 
<code>sum (container_memory_usage_bytes{pod=~"comdemo.*"}) by (pod)</code> would collect only memory utilized by the comdemo Emerald pods
>Further examples of Prometheus queries can be found by browsing the SoFy Monitoring page which presents a Grafana dashboard, pre-configured with a range of queries and charts.

Once the resource monitors are created and labelled, move back to the Execution page and execute the performance test BenchmarkB2CFlow. Open the Resource Monitoring tab and select one or more of the counters you configured before starting the test. At this point, the test will take around a minute to start running and approximately 10 minutes to complete. You may wish to show an existing report and walk through the different reports and charts, highlighting the 'Resources' chart that includes the counters we just configured in Prometheus

## **Power of the API**

At this point, your customer may want to go deeper into the test authoring experience, but it is important first to put what they have seen into context. Few customers will use OneTest Server soley to trigger tests manually in the dashboard - far more likely, they will use external services to trigger execution headlessly as part of a continuous test/deploy or DevOps pipeline.

OneTest Server integrates natively via plugins to several different platforms (HCL Launch, Jenkins, Azure DevOps etc) but also exposes execution APIs to allow for any toolchain integration.

Swagger documentation for the APIs is provided and can be accessed (as a OneTest Server admin user) via the URL <code>[https://onetestserverhostname/#/admin/docs](https://10.134.59.19.nip.io/#/admin/docs)</code>

Executing a test via APIs is a two step process
1. Pass an 'offline token' to the authentication service and retrieve an 'access token'
2. Make a request to the jobs service to execute a given test configuration, passing the access token

<details><summary>Curl templates</summary>

AuthToken Request:

<code>curl -X POST -H "Accept: application/json" -d refresh_token="__offline token from OTS__" https://onetestserverhostname/rest/tokens/ -k</code>

Sample Response (extract access token value):

<code>{"access_token":"__access token__","scope":"testserver email offline_access profile","session_state":"a43cac4e-9d0b-40f7-8681-76ecd6ef4d23","token_type":"bearer"}</code>

Execution Request:

*  Replace Bearer value with access_token value and offlineToken with the server offline token. 
*  assetId is unique to your environment and can be found using the developer tools in your browser when executing a test
*  tags takes the value you wish to use when labelling the test execution, use this to mimic an external tool execution ID
*  environment name if required by API & Postman tests only
*  project id is unique to your environment and can be found using the developer tools in your browser when executing a test

<code>Curl -H "Content-Type: application/json" -H "Authorization: Bearer __access token__" -d '{"testAsset":{"assetId":"__asset id__","revision":"main","requestedVersion":null},"tags":["__Build ID 000148__"],"environment":"__environment name__","remoteLocations":[],"offlineToken":"__offline token from OTS__"}' https://onetestserverhostname/rest/projects/__projectid__/executions/</code>

</details>

