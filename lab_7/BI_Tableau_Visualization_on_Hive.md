#MIDS W205

| Lab # | 8 | Lab Title | Introduction to Using Tableau With Hive |
|:---|:---|:---|:---|
| **Related Module(s)** | 9 | **Goal** | Introduction to Using Tableau With Hive |
| **Last Updated** | 10/14/15 | **Expected Duration** | 30- ­40 minutes |

##BI Tableau Visualization Using Hive Warehouse

###Introduction

Tableau is one of the world’s fastest-growing business intelligence companies. Tableau offers to quickly analyze, visualize, and share information. As with most BI tools, Tableau can use Apache Hive (via ODBC connection) as the de facto standard for SQL access in Hadoop.
We will cover the following topics in this lab:
- Creating a Hive table and running a sample query on Hive
- Installing Tableau and ODBC driver for connecting to Hive
- Configuring and connecting to Hadoop Hive from Tableau using ODBC driver
- Build visualization on Weblog Clickstream Analytics using Tableau

###Step 1: Creating Hive Table and Running a Sample Query on Hive

The following code creates a table—Web_Session_Log—from the weblog data introduced in previous labs. While this lab uses Tableau to explore the weblog data, feel free to proceed with any table you want.

  ```sql
  CREATE TABLE Web_Session_Log(
    DATETIME varchar(500),
    USERID varchar(500),
    SESSIONID varchar(500),
    PRODUCTID varchar(500), 
    REFERERURL varchar(500) 
    ) 
  ROW FORMAT DELIMITED FIELDS TERMINATED BY "\t" 
  STORED AS textfile 
  tblproperties("skip.header.line.count"="1");
  ```

####Load Data into the Table:

From HDFS:

  ```sql
  LOAD DATA INFILE `/mnt/weblog/weblog.csv'
  INTO TABLE Web_Session_Log;
  ```

From Local File System:

  ```
  LOAD DATA LOCAL INFILE `/mnt/weblog/weblog.csv'
  INTO TABLE Web_Session_Log;
  ```

####Sample Hive Query

This is a query to find out which `REFERERURL` has been referred and for how many times.

  ```sql
  SELECT REFERERURL, count(*)
  FROM Web_Session_Log
  GROUP BY REFERERURL;
  ```

Pleaseseebelowfortheexactcode:

![extract code](screenshots/lab7_img1.png)

### Step 2: Starting a Hive Thrift Server for Remote Hive Access

HiveServer2 is a server interface that allows remote clients to execute queries against Hive. In Tableau, we will be able to extract data from our `Web_Session_Log` table by sending requests through HiveServer2.

####How to start:

  ```
  hive ­service hiveserver2
  ```

or

  ```
  hive ­service hiveserver ­help
  hive ­service hiveserver
  ```

(Note that as of Hive 1.0.0, HiveServer was removed in favor of HiveServer2. It is recommended you use HiveServer2.)

####Here is a connection string for your reference:

with username and password:

  ```
  jdbc:hive2://myhost.example.com:21050/test_db;user=fred;password=xyz123
  ```

without username and password:

  ```
  jdbc:hive2://ec2-54-157-182-212.compute-1.amazonaws.com:10000/default
  ```

###Step 3: Installing Tableau and ODBC Driver for Connecting to Hive

Hive is a data warehouse technology for working with data in your Hadoop cluster using a combination of traditional SQL expressions and advanced Hadoop-specific data analysis and transformation operations. Tableau works with Hadoop using Hive to provide a user experience that requires no programming.

In this lab, we will connect Tableau Desktop Pro with the HiveServer in order to access the Hive table `Web_Session_Log`, which we created earlier.


####To install Tableau on your desktop (Tableau Desktop Pro Edition):

You can download Tableau Desktop Pro directly from Tableau’s [website](http://www.tableau.com/). Select the Windows or Mac version based on your operating system (32-bit or 64-bit).

Once the product is downloaded, you can install it by double-clicking the installer package.

![tableau desktop](screenshots/lab7_img2.png)

####To install the ODBC driver for Hive:

For both HiveServer and HiveServer2, you must install the Cloudera, Hortonworks, MapR, or Amazon EMR ODBC driver from the Drivers page. Ensure that the version of the driver you download matches the bit version of your operating system.

- Cloudera (Hive): Cloudera ODBC Driver for Apache Hive 2.5.x, 32-bit or 64-bit
  - For use with Tableau Server 8.0.0–8.0.7; for 8.1.0–8.1.3, use version 2.5.0.1000. 
  - For use with Tableau Server 8.0.8 and later; for 8.1.4 and later, use driver version 2.5.0.1001 or later.
  - Cloudera drivers can be found here.

- Hortonworks: Hortonworks Hive ODBC Driver 1.2.x (32-bit or 64-bit)
- MapR: MapR_odbc_2.1.0_x86.exe or later, or MapR_odbc_2.1.0_x64.exe or later
- Amazon EMR: HiveODBC.zip or ImpalaODBC.zip

**Note**: If you have a different version of the driver installed, uninstall that driver before installing the version provided on the Cloudera website.


###Step 4: Configuring and Connecting to Hadoop Hive From Tableau Using ODBC Driver (Windows Only)

After installing the appropriate ODBC driver, you need to configure the ODBC System DSN on Windows. Download the driver for the appropriate version of Tableau Desktop (x86 or x64), and install it. Go to Control Panel -> System & Security -> Administrative Tools -> ODBC Data sources (32-bit or 64-bit).

Open the ODBC data source, go to the System DSN tab, and configure it by applying Host (Hadoop server name), Port (10000), Database(default), HiveServer Type (HiveServer2), and the authentication panel, then enter the username and password credentials (if you configured security settings on server authentication).

![odbc driver](screenshots/lab7_img3.png)

Click **Test** to check the connectivity of Tableau to the Hadoop server. If the connection is successful, a success message is displayed. Otherwise, an error message is displayed.

![test results](screenshots/lab7_img4.png)

###Step 5: Connect Tableau to HiveServer/HiveServer2 Using ODBC Driver

Open Tableau Desktop Pro from its quick-launch option.

![tableau new](screenshots/lab7_img5.png)

Next, go to Data -> New Data Source tab, and click **Cloudera Hadoop** to connect to the Hadoop server.

![select cloudera hadoop](screenshots/lab7_img6.png)

The Cloudera Hadoop connection pane opens. Enter the Hadoop server credentials to connect. To connect to the HiveServer, the default port number should be 10000, the Type should be HiveServer2, and the username needs to be provided.

![cloudera hadoop server](screenshots/lab7_img7.png)


###Step 6: Build Visualizations on Weblog, Clickstream Analytics Using Tableau

After the data source connection is made successfully, you can connect to the Hive table. Click **Extract** to get data locally and store Tableau in memory reduce latency because Hive is based on a batch-processing mechanism. Switch back to Tableau. Under the Data Connection pane, enter the schema name, enter **default**, and click the **Search** icon on the right side of the textbox. Next, enter the table name Web_Session_Log in the Table name textbox, click the **Search** icon on the right side of the textbox, and select and drag the table to the upper-right side of the Tableau window, as shown in the following screenshot.

![](screenshots/lab7_img8.png)

Now you can extract the data locally on an in-memory Tableau dataset to avoid unnecessary server latency.

![](screenshots/lab7_img9.png)

Click **Automatic update** to refresh the dataset. It should look like the following screen:

![](screenshots/lab7_img10.png)

Click the **Worksheet** tab, and select **New Worksheet** to start building visualizations.
On the new worksheet tab, on the Data pane, drag the `web_session_log.sessionid` field to the Measures pane.

![](screenshots/lab7_img11.png)

Next, drag the field to the **Rows** shelf. Hover over the field to expand the drop-down menu. Change the aggregation method (under Measure) from Sum to Count (Distinct).

![](screenshots/lab7_img12.png)

Drag the `web_session_log.refererurl` field to the Column shelf, then right-click the value to create a filter to visualize the top five referring URLs. Select **Filter**, and click the **Condition** tab to get URLs based on `web_session_log.sessionids` beyond a certain value (e.g., `web_session_log.sessionid` >= 3,996) to drill down to the top five referring URLs.

![](screenshots/lab7_img13.png)

After you define the settings, change the chart type in the Show Me pane on the right side of the window.

![](screenshots/lab7_img14.png)

Select the bubbles chart type. You will see the graph, “Top 5 Referring URLs,” as follows:

![](screenshots/lab7_img15.png)

Next, create a new worksheet and name it **Top Referring URLs over last 10 years**. Drag `web_session_log.sessionid` to the Rows shelf.
On the Data pane, right-click on the datetime field, and change its data type to Date and Time. Then, drag it to the Columns shelf. Drag the Referer URL field to the Color section of the Marks pane. In the Show Me section, select the lines chart type.

![](screenshots/lab7_img16.png)

Go to a new worksheet tab, and name it **Top 10 users who used top 10 products**. On this worksheet tab, drag the `sessionid` field to the Rows shelf and the `userid` field to the Columns shelf. Filter the users based on the maximum session count. You need to filter the `userid` data based on condition of the `sessionid` value. Drag the `productID` field to the color field in the Marks pane to differentiate between products. You may select the simple stacked-bar chart type.

![](screenshots/lab7_img17.png)

Once, you build such few graphs, click the **Dashboard** tab on top of the Tableau window.

![](screenshots/lab7_img18.png)

Select **New Dashboard** to start implementing the **weblog-clickstream analytics** dashboard. On the new dashboard page, drag the existing implemented worksheet graphs to the right pane.

![](screenshots/lab7_img19.png)

When you drag and drop the implemented graphs on the exiting worksheet, you will see the Weblog Clickstream analytics dashboard on Tableau Desktop, as shown in the following screenshot:

![](screenshots/lab7_img20.png)

You can also publish the workbook on Tableau Server if you have those credentials. Always save the workbook on your local drive using the File -> Export as Packaged workbook option. You can also print the entire workbook or selected worksheets in PDF format as required.

![](screenshots/lab7_img21.png)

The Tableau **weblog-hive-clickstream analytics workbook** is printed in PDF format.

![](screenshots/lab7_img22.png)

Task: Submit the printed PDF **weblog-hive-clickstream analytics** workbook.
