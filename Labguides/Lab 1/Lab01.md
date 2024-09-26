# Lab 01: Ingesting Data using Real-Time Intelligence

## Introduction

In this lab, you will start by rapidly generating real-time data and
understanding how that data can be processed and visualized in Microsoft
Fabric. With the initial reporting in place, multiple modules are
available that explore data warehousing, data lakehouse architecture,
data activator, data science, and of course, real-time analytics. The
modules are designed to be cohesive but flexible -- they all involve the
same core scenario but have limited dependencies so you can consume the
modules that make the most sense for you.

The basic architecture of the solution is illustrated below. The app
deployed in the beginning of this lab (either as a docker container or
running in Jupyter notebook) will publish events to our Fabric
environment. The data is ingested into a KQL database for real-time
reporting in Power BI.

In this lab, you’ll get hands-on with a fictitious financial company
"AbboCost." AbboCost would like to set up a stock monitoring platform to
monitor price fluctuations and report on historical data. Throughout the
workshop, we'll look at how every aspect of Microsoft Fabric can be
incorporated as part of a larger solution -- by having everything in an
integrated solution, you'll be able to quickly and securely integrate
data, build reports, create data warehouses and lakehouses, forecast
using ML models, and more.

<img src="./media/image1.png" style="width:6.5in;height:3.65625in"
alt="Data Lakehouse with Azure Synapse Analytics" />

# Objectives

- To sign up for the free Microsoft Fabric trial, redeem Azure Pass, and
  configure necessary permissions within the Azure portal.

- To create fabric capacity and workspace, storage account, and fabric
  workspace.

- To deploy the stock generator app via Azure Container Instance using
  an ARM template.

- To configure Eventstream in Microsoft Fabric for ingesting real-time
  data from Azure Event Hubs, ensuring seamless integration and data
  preview for subsequent analysis.

- To create a KQL database within Microsoft Fabric and send data from
  Eventstream to the KQL database.

# Exercise 1: Environment Setup

To follow the lab exercises, a set of resources must be provisioned. At
the heart of the scenario is the real-time stock price generator script
that generates a continuous stream of stock prices that are used
throughout the workshop.

We recommend deploying the stock price generator via Azure Container
Instance because the default Spark cluster will consume a large number
of resources.

## Task 1: Sign in to Power BI account and sign up for the free [Microsoft Fabric trial](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial)

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then
    press the **Enter** button.

> <img src="./media/image2.png" style="width:6.5in;height:2.89653in"
> alt="A search engine window with a red box Description automatically generated with medium confidence" />

2.  In the **Microsoft Fabric** window, enter your given credentials and
    click on the **Submit** button.

> <img src="./media/image3.png" style="width:6.49167in;height:3.11667in"
> alt="A close up of a white and green object Description automatically generated" />

3.  Enter the **password** from the **Resources** tab and click on the
    **Sign in** button**.**

> <img src="./media/image4.png" style="width:4.50833in;height:3.83333in"
> alt="A login screen with a red box and blue text Description automatically generated" />

4.  In **Stay signed in?** window, click on the **Yes** button.

> <img src="./media/image5.png" style="width:4.58333in;height:3.66667in"
> alt="A screenshot of a computer error Description automatically generated" />

5.  You’ll be directed to Power BI Home page.

> <img src="./media/image6.png" style="width:5.8956in;height:2.72357in"
> alt="A screenshot of a computer Description automatically generated" />

## Task 2: Start the Microsoft Fabric trial

1.  On **Power BI Home** page, click on the **Account manager** **for
    MOD Administrator** icon on the top right corner of the page. In the
    Account manager blade, navigate and select **Start trial** as shown
    in the below image**.**

> <img src="./media/image7.png"
> style="width:6.49167in;height:4.43333in" />

2.  On **Upgrade to a free Microsoft Fabric** trial dialog box, click on
    **Start trial** button**.**

> <img src="./media/image8.png"
> style="width:6.13068in;height:2.43655in" />

3.  You will see a **Successfully upgraded to a free Microsoft Fabic
    trial** notification dialog box. In the dialog box, click on
    **Fabric Home Page** button.

> <img src="./media/image9.png" style="width:5.41667in;height:1.85in" />
>
> <img src="./media/image10.png" style="width:6.5in;height:2.3875in"
> alt="A screenshot of a computer Description automatically generated" />

## **Task 3: Create a Fabric workspace**

In this task, you create a Fabric workspace. The workspace contains all
the items needed for this lakehouse tutorial, which includes lakehouse,
dataflows, Data Factory pipelines, the notebooks, Power BI datasets, and
reports.

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then
    press the **Enter** button. In the **Microsoft Fabric Home** page,
    navigate and click on **Power BI** tile.

> <img src="./media/image11.png" style="width:4.87083in;height:4.73327in"
> alt="A screenshot of a computer Description automatically generated" />

2.  In the **Power BI Home** page left-sided navigation menu, navigate
    and click on **Workspaces** as shown in the below image.

> <img src="./media/image12.png" style="width:6.5in;height:6.85833in" />

3.  In the Workspaces pane, click on **+** **New workspace button**

> <img src="./media/image13.png" style="width:4.41667in;height:7.7in"
> alt="A screenshot of a computer Description automatically generated" />

4.  In the **Create a workspace** pane that appears on the right side,
    enter the following details, and click on the **Apply** button.

| **Name** | **+++RealTimeWorkspaceX*XX*+++** (XXX can be a unique number, you can add more numbers) |
|----|----|
| **Advanced** | Select Trail |
| **Default storage format** | **Small dataset storage format** |

> <img src="./media/image14.png"
> style="width:6.14375in;height:5.77292in" />
>
> <img src="./media/image15.png"
> style="width:5.48333in;height:6.87917in" />

## **Task 4: Deploy the app via Azure Container Instance**

This task deploys the stock generator app to an Azure Container Instance
using an ARM template. The app will generate stock data that publishes
the data to an Azure Event Hub, which is also configured during the
deployment of the ARM template.

To auto-deploy the resources, use these steps below.

1.  Open a new address bar and enter the following URL. If prompted to
    Sign in, then use your O365 tenant credentials.

> +++https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Ffabricrealtimelab%2Fmain%2Fresources%2Fmodule00%2Ffabricworkshop_arm_managedid.json+++

2.  In the **Custom deployment** window, under the **Basics** tab, enter
    the following details and click on the **Review+create** button.

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 74%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>Subscription</strong></th>
<th><blockquote>
<p>Select the assigned subscription</p>
</blockquote></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>Resource group</strong></td>
<td>Click on <strong>Create new</strong>&gt; enter
+++<strong>realtimeworkshop+++</strong> and select
<strong>Ok</strong></td>
</tr>
<tr class="even">
<td><strong>Region</strong></td>
<td>Select <strong>West US 3</strong></td>
</tr>
</tbody>
</table>

> <img src="./media/image16.png" style="width:5.6875in;height:5.81892in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image17.png" style="width:5.59in;height:5.19583in"
> alt="A screenshot of a computer Description automatically generated" />

3.  In the **Review + create** tab, navigate and click on the **Create**
    button**.**

> <img src="./media/image18.png" style="width:6.5in;height:5.91667in" />

5.  Wait for the deployment to complete. The deployment will take around
    10-15 minutes.

6.  After the deployment is completed, click on the **Go to resource**
    button.

> <img src="./media/image19.png" style="width:6.49167in;height:3.04167in"
> alt="A screenshot of a computer Description automatically generated" />

4.  In **realtimeworkshop** **Resource group**, verify that the **Event
    Hub Namespace **and **Azure Container Instance (ACI)** are
    successfully deployed.

> <img src="./media/image20.png"
> style="width:6.30903in;height:3.35294in" />

5.  Open the **Event Hub** **namespace**, which will have a name similar
    to** *ehns-XXXXXX-fabricworkshop***.

> <img src="./media/image21.png" style="width:6.25in;height:3.22917in" />

6.  In **Event Hub** **namespace** page left-sided navigation menu,
    navigate to **Settings** section and click on **Shared access
    policies**.

> <img src="./media/image22.png" style="width:6.28787in;height:5.27083in"
> alt="A screenshot of a computer Description automatically generated" />

7.   In the ***Shared access policies*** page, click on
    ***stockeventhub_sas*** .**SAS Policy: stockeventhub_sas** pane
    appear on the right side, copy the **primary key** and **Event Hub
    namespace** (such as *ehns-XXXXXX-fabricworkshop*) and paste them on
    a notepad, as you need them in the upcoming task. In short, you'll
    need the following:

> <img src="./media/image23.png" style="width:6.5in;height:3.27083in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image24.png" style="width:5.40417in;height:5.10485in"
> alt="A screenshot of a computer Description automatically generated" />

## **Task 5: Get data with Eventstream**

1.  Go back to the Microsoft Fabric, navigate and click on **Power BI**
    at the bottom of the page, then select **Real-Time Intelligence**.

<img src="./media/image25.png" style="width:5.71667in;height:7.59167in"
alt="A screenshot of a computer Description automatically generated" />

2.  On the **Synapse Real-Time Analytics** home page,
    select **Eventstream**. Name the Eventstream
    +++ ***StockEventStream**+++, check the **Enhanced Capabilities
    (preview)*** and click on the **Create** button.

<img src="./media/image26.png" style="width:6.49167in;height:4.28333in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image27.png" style="width:3.39167in;height:2.9in"
alt="A screenshot of a computer Description automatically generated" />

3.  On the Eventstream, select **Add external source**

> <img src="./media/image28.png"
> style="width:6.49167in;height:3.79167in" />

4.  On the Add source, select **Azure *Event Hubs.***

> <img src="./media/image29.png" style="width:6.49167in;height:2.99167in"
> alt="A screenshot of a chat Description automatically generated" />

5.  On the **Azure Event Hubs** configuration page, enter the below
    details and click on **Add** button.

<!-- -->

1.  Configure connection settings: Click on the **New connection** and
    enter the below details then click on **Create** button.

<!-- -->

1.  In Event Hub namespace-Enter Event Hub name (the values that you
    have saved in your notepad**)**

2.  Event Hub : **+++StockEventHub+++**

3.  Shared Access Key Name:+++**stockeventhub_sas+++**

4.  Shared Access Key- Enter Primary Key (the value that you have saved
    in your notepad in the **Task 4)**

<!-- -->

2.  Consumer group: ***\$Default*** 

3.  Data format: **JSON** and click on **Next** button

> <img src="./media/image30.png" style="width:6.4314in;height:3.1125in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image31.png" style="width:6.81853in;height:3.7375in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image32.png" style="width:6.34722in;height:3.65026in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image33.png" style="width:6.3625in;height:3.03832in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image34.png" style="width:6.56977in;height:3.15417in"
> alt="A screenshot of a computer Description automatically generated" />

6.  You will see a notification stating **Successfully added The source
    “StockEventHub,Azure Event Hubs”** was added.

> <img src="./media/image35.png" style="width:4.17536in;height:2.61689in"
> alt="A screenshot of a computer Description automatically generated" />

7.  With the Event Hub configured, click on ***Test result***. You
    should see events including the stock symbol, price, and timestamp.

> <img src="./media/image36.png" style="width:7.0667in;height:4.3125in" />

8.  On the Eventstream, select **Publish.**

> <img src="./media/image37.png"
> style="width:7.1573in;height:4.34583in" />
>
> <img src="./media/image38.png"
> style="width:6.86169in;height:4.14341in" />

9.  On the Eventstream, select **AzureEventHub** and click on
    **Refresh** button.

> <img src="./media/image39.png" style="width:6.5in;height:4.09167in" />
>
> <img src="./media/image40.png" style="width:6.5in;height:4.08889in"
> alt="A screenshot of a computer Description automatically generated" />

# Exercise 2: KQL Database Configuration and Ingestion

Now that our environment is fully configured, we will complete the
ingestion of the Eventstream, so that the data is ingested into a KQL
database. This data will also be stored in Fabric OneLake.

## Task 1: Create KQL Database

Kusto Query Language (KQL) is the query language used by Real-Time
analytics in Microsoft Fabric along with several other solutions, like
Azure Data Explorer, Log Analytics, Microsoft 365 Defender, etc. Similar
to Structured Query Language (SQL), KQL is optimized for ad-hoc queries
over big data, time series data, and data transformation.

To work with the data, we'll create a KQL database and stream data from
the Eventstream into the KQL DB.

1.  In the left-sided navigation menu, navigate and click on **RealTime
    workspaceXXX**, as shown in the below image.

> <img src="./media/image41.png"
> style="width:3.12083in;height:5.12655in" />

2.  In the **Real-Time Intelligence** page, navigate to **+New item**
    section and click on, select **Eventhouse** to create Eventhouse.

> <img src="./media/image42.png" style="width:6.49167in;height:4.275in" />

3.  In the **New Eventhouse** dialog box, enter +++**StockDB+++** in
    the **Name** field, click on the **Create** button and open the new
    Eventhouse.

> <img src="./media/image43.png" style="width:3.53056in;height:2.09861in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image44.png" style="width:6.5in;height:3.75417in"
> alt="A screenshot of a computer Description automatically generated" />

4.  Select StockDB, click on the **OneLake availability** as shown in
    the below image to change the setting and, then click on the **Turn
    on** button to enable OneLake access.

> <img src="./media/image45.png"
> style="width:5.75947in;height:4.78341in" />
>
> <img src="./media/image46.png"
> style="width:3.64375in;height:6.13611in" />
>
> <img src="./media/image47.png" style="width:3in;height:1.5in" />

5.  After enabling OneLake, you may need to refresh the page to verify
    the OneLake folder integration is active.

> <img src="./media/image48.png" style="width:4.90341in;height:2.65341in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image49.png" style="width:6.5in;height:3.54792in"
> alt="A screenshot of a computer Description automatically generated" />

## Task 2: Send data from the Eventstream to the KQL database

1.  In the left-sided navigation menu, navigate and click on
    **StockEventStream** created in the previous task, as shown in the
    below image.

> <img src="./media/image50.png" style="width:6.5in;height:5.975in"
> alt="A screenshot of a computer Description automatically generated" />

2.  On the Eventstream, click on the **Edit** button.

> <img src="./media/image51.png" style="width:6.5in;height:3.78819in" />

3.  Our data should be arriving into our Eventstream, and we'll now
    configure the data to be ingested into the KQL database we created
    in the above task. On the Eventstream, click on *Transform events or
    add destination,* then navigate and click on **Eventhouse**.

> <img src="./media/image52.png"
> style="width:6.49236in;height:3.72708in" />

4.  On the KQL settings, select ***Direct ingestion***. While we have
    the opportunity to process event data at this stage, for our
    purposes, we will ingest the data directly into the KQL database.
    Set the destination name to +++***KQL**+++*, then select your
    **workspace, Eventhouse** and KQL database created in the above
    task, then click on **Save** button.

> <img src="./media/image53.png"
> style="width:2.81042in;height:5.02292in" />

5.  Click on the **Publish** button

> <img src="./media/image54.png" style="width:6.5in;height:3.49236in" />
>
> <img src="./media/image55.png" style="width:4.13369in;height:2.3502in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image56.png" style="width:6.5in;height:3.26319in"
> alt="A screenshot of a computer Description automatically generated" />

6.  On the Eventstream pane, select **configure** in the **KQL**
    destination.

> <img src="./media/image57.png" style="width:6.5in;height:3.46667in"
> alt="A screenshot of a computer Description automatically generated" />

7.  On the first settings page, select **+New table** and enter the
    name +++***StockPrice**+++* for the table to hold the data in
    StockDB. Click on the **Next** button.

<img src="./media/image58.png" style="width:6.49167in;height:3.84167in"
alt="A screenshot of a computer Description automatically generated" />

<img src="./media/image59.png" style="width:6.5in;height:3.78333in"
alt="A screenshot of a computer Description automatically generated" />

8.  The next page allows us to inspect and configure the schema. Be sure
    to change the format from TXT to **JSON**, if necessary. The default
    columns of *symbol*, *price*, and *timestamp* should be formatted as
    shown in the below image; then click on the *Finish* button.

> <img src="./media/image60.png" style="width:6.57066in;height:3.8125in"
> alt="A screenshot of a computer Description automatically generated" />

2.  On the **Summary** page, if there are no errors, you’ll see a
    **green checkmark** as shown in the below image, then click on the
    *Close* button to complete the configuration.

> <img src="./media/image61.png" style="width:5.9125in;height:3.45339in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image62.png" style="width:6.77947in;height:3.53387in"
> alt="A screenshot of a computer Description automatically generated" />

9.  Click on the **Refresh** button

> <img src="./media/image63.png" style="width:6.34042in;height:3.29636in"
> alt="A screenshot of a computer Description automatically generated" />

10. Select the **KQL** destination and click on the **Refresh** button.

> <img src="./media/image64.png" style="width:6.6191in;height:3.67917in"
> alt="A screenshot of a computer Description automatically generated" />
>
> <img src="./media/image65.png" style="width:6.34019in;height:3.07729in"
> alt="A screenshot of a computer Description automatically generated" />

**Summary**

In this lab, you’ve signed up for the Microsoft Fabric trial and
redeemed Azure Pass, followed by configuring permissions and creating
necessary resources within the Azure portal such as Fabric Capacity,
Workspace, and Storage Account. Then, you’ve deployed the stock
generator app via Azure Container Instance using an ARM template to
generate real-time stock data. Additionally, you’ve configured
Eventstream in Microsoft Fabric to ingest data from Azure Event Hubs and
prepared the KQL Database to store this data efficiently. In this lab,
you’ve established a fully functional environment to proceed with
subsequent lab exercises related to real-time analytics and data
processing.