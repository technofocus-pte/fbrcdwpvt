# Use Case 4 - Monitoring activity and queries of data warehouse in Microsoft Fabric

**Introduction**

In Microsoft Fabric, a data warehouse provides a relational database for
large-scale analytics. Data warehouses in Microsoft Fabric include
dynamic management views (DMVs) that you can use to monitor activity and
queries. This document outlines the steps to set up a workspace, create
a sample data warehouse, and utilize various tools to monitor and manage
the data warehouse effectively.

**Objective:**

  - Set up a new workspace in Microsoft Fabric to organize and manage
    your data projects..

  - Develop a sample data warehouse within the Microsoft Fabric
    workspace to store and analyze data.

  - Investigate the dynamic management views available in Microsoft
    Fabric to monitor and manage your data warehouse.

  - Utilize the query insights feature to analyze and optimize queries
    within your data warehouse.

**Note**: You need a [Microsoft Fabric
trial](https://learn.microsoft.com/fabric/get-started/fabric-trial) to
complete this Use Case.

## Task 1: Create a workspace

Before working with data in Fabric, create a workspace with the Fabric
trial enabled.

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: https://app.fabric.microsoft.com/ then press the
    **Enter** button.

     ![](./media/image1.png)

2.  In the **Microsoft Fabric** window, enter assigned credentials, and
    click on the **Submit** button.

     ![](./media/image2.png)

3.  Then, In the **Microsoft** window enter the password and click on
    the **Sign in** button . 

     ![](./media/image3.png)

4.  In **Stay signed in?** window, click on the **Yes** button.

     ![](./media/image4.png)

5.  On the **Microsoft Fabric** home page, select the **Power BI**
    template.

    ![](./media/image5.png)

6.  In the **Power BI Home** page menu bar on the left,
    select **Workspaces** (the icon looks similar to 🗇).

    ![](./media/image6.png)

7.  In the Workspaces pane Select **+** **New workspace**.

     ![](./media/image7.png)

8.  In the **Create a workspace tab**, enter the following details and
    click on the **Apply** button.

      |   |   |
      |---|---|
      |Name|	+++dp-FabricXX+++ (XX can be a unique number) |
      |Advanced|	Under License mode, select Trial|
      |Default storage format|	Small dataset storage format|


      ![](./media/image8.png)
      
      ![](./media/image9.png)
      
      ![](./media/image10.png)

9.  Wait for the deployment to complete. It takes 2-3 minutes to
    complete. When your new workspace opens, it should be empty.

     ![](./media/image11.png)

10.  In the **Power BI dp_FabricXX**page, click on the **Data
    Warehouse** icon located at the bottom left and select **Data
    Warehouse** under Datascience..
    ![](./media/image12.png)

## Task 2:Create a sample data warehouse

Now that you have a workspace, it’s time to create a data warehouse.

1.  At the bottom left, ensure that the **Data Warehouse** experience is
    selected.

2.  On the **Home** page, select **Sample warehouse** and create a new
    data warehouse named +++sample-dw+++.

     ![](./media/image13.png)
 
     ![](./media/image14.png)
  
     ![](./media/image15.png)

3.  After a minute or so, a new warehouse will be created and populated
    with sample data for a taxi ride analysis scenario.

    ![](./media/image16.png)

## Task 3: Explore dynamic management views

Microsoft Fabric data warehouses include dynamic management views
(DMVs), which you can use to identify current activity in the data
warehouse instance.

1.  In the **sample-dw** data warehouse page, in the **New SQL
    query** drop-down list, select **New SQL query**.

     ![](./media/image17.png)

2.  In the new blank query pane, enter the following Transact-SQL code
    to query the **sys.dm_exec_connections** DMV:

     sqlCopy
    
    +++SELECT * FROM sys.dm_exec_connections;+++

3.  Use the **▷ Run** button to run the SQL script and view the results,
    which include details of all connections to the data warehouse.

    ![](./media/image18.png)
    
    ![](./media/image19.png)

4.  Modify the SQL code to query the **sys.dm\_exec\_sessions** DMV,
    like this:

    sqlCopy
    
    +++SELECT \* FROM sys.dm\_exec\_sessions;+++

5.  **Run** the modified query and view the results, which show details
    of all authenticated sessions

     ![](./media/image20.png)

6.  Modify the SQL code to query the **sys.dm_exec_requests** DMV,
    like this:

     sqlCopy
     
     +++SELECT * FROM sys.dm_exec_requests;+++
7.  Run the modified query and view the results, which show details of
    all requests being executed in the data warehouse.

    ![](./media/image21.png)

8.  Modify the SQL code to join the DMVs and return information about
    currently running requests in the same database, like this:
      ```
      SELECT connections.connection_id,
       sessions.session_id, sessions.login_name, sessions.login_time,
       requests.command, requests.start_time, requests.total_elapsed_time
      FROM sys.dm_exec_connections AS connections
      INNER JOIN sys.dm_exec_sessions AS sessions
          ON connections.session_id=sessions.session_id
      INNER JOIN sys.dm_exec_requests AS requests
          ON requests.session_id = sessions.session_id
      WHERE requests.status = 'running'
          AND requests.database_id = DB_ID()
      ORDER BY requests.total_elapsed_time DESC;
      ```
9.  **Run** the modified query and view the results, which show details
    of all running queries in the database (including this one).

    ![](./media/image22.png)

10. In the **New SQL query** drop-down list, select **New SQL query** to
    add a second query tab.

      ![](./media/image23.png)

11. In the new empty query tab, run the following code:
    ```
    WHILE 1 = 1
        SELECT * FROM Trip;
    
    ```
    ![](./media/image24.png)

12. Leave the query running, and return to the tab containing the code
    to query the DMVs and re-run it. This time, the results should
    include the second query that is running in the other tab. Note the
    elapsed time for that query.

      ![](./media/image25.png)

13. Wait a few seconds and re-run the code to query the DMVs again. The
    elapsed time for the query in the other tab should have increased.

14. Return to the second query tab where the query is still running and
    select **X Cancel** to cancel it.

      ![](./media/image26.png)

15. Back on the tab with the code to query the DMVs, re-run the query to
    confirm that the second query is no longer running.

    ![](./media/image27.png)

16. Close all query tabs.

## Task 4: Explore query insights

Microsoft Fabric data warehouses provide *query insights* - a special
set of views that provide details about the queries being run in your
data warehouse.

1.  In the **sample-dw** data warehouse page, in the **New SQL
    query** drop-down list, select **New SQL query**.

      ![](./media/image23.png)

2.  In the new blank query pane, enter the following Transact-SQL code
    to query the **exec_requests_history** view:

3.  Use the **▷ Run** button to run the SQL script and view the results,
    which include details of previously executed queries.

     +++SELECT * FROM queryinsights.exec_requests_history;+++

    ![](./media/image28.png)

4.  Modify the SQL code to query the **frequently\_run\_queries** view,
    like this:

 
    +++SELECT * FROM queryinsights.frequently_run_queries;+++

5.  Run the modified query and view the results, which show details of
    frequently run queries

    ![](./media/image29.png)

6.  Modify the SQL code to query the **long\_running\_queries** view,
    like this:

    +++SELECT * FROM queryinsights.long_running_queries;+++

7.  Run the modified query and view the results, which show details of
    all queries and their durations.

    ![](./media/image30.png)

## Task 5:Clean up resources

In this exercise, you have used dynamic management views and query
insights to monitor activity in a Microsoft Fabric data warehouse.

If you’ve finished exploring your data warehouse, you can delete the
workspace you created for this exercise.

1.  In the bar on the left, select the icon for your workspace to view
    all of the items it contains.

      ![](./media/image31.png)

2.  In the **…** menu on the toolbar, select **Workspace settings**.

    ![](./media/image32.png)

3.  In the **General** section, select **Remove this workspace**.

    ![](./media/image33.png)
    
    ![](./media/image34.png)

**Summary:**

This use case provides a comprehensive guide to monitoring activity and
queries in a Microsoft Fabric data warehouse. It covers setting up a new
workspace, creating a sample data warehouse, and using dynamic
management views (DMVs) to monitor current activity, including
connections, sessions, and requests. Additionally, it explains how to
utilize the query insights feature to analyze previously executed,
frequently run, and long-running queries. Finally, it includes steps to
clean up resources by deleting the workspace after completing the
exercise. This guide is designed to help users effectively manage their
data warehouses in Microsoft Fabric, leveraging built-in tools for
optimal performance and insights.
