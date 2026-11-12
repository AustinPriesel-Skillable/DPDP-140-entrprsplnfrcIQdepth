# Lab 01: Build and optimize a sales plan on a Power BI semantic model with Plan in Microsoft Fabric

## Scenario

Acme Beverages is a global beverage company that sells beer, hard seltzer, and other drinks across North America, APAC, the EU, and Latin America. Leadership has asked Finance and Sales to prepare the 2026 plan. Finance will set a top-down revenue target, weighting growth toward the second half of the year. Sales will build a bottom-up plan from 2025 actuals, including an agreed uplift for Summit Lager in North America and a summer promotion for Beer and Hard Seltzer in the EU and Latin America. The CFO has also set a \$12.5B gross profit goal, so the team must work out how much revenue growth and cost reduction are needed to reach it. Finally, plan changes must be routed through Regional Manager and Finance Director approval before the plan is finalized.

To bring Finance and Sales together around one governed source of data, Acme Beverages plans directly on top of its Power BI semantic model in Microsoft Fabric. The solution uses the following key components:
- **Acme Beverages semantic model**: Provides the governed 2025 actuals,
  dimensions, and measures that the plan is built on.

- **Fabric SQL database**: Stores plan changes and enables collaboration
  for workspace viewers.

- **Plan (preview) app**: Hosts the planning sheets where you set
  targets, distribute values, lock cells, and apply Bulk Edit uplifts.

- **Optimizer**: Finds the mix of revenue growth and cost reduction that
  meets the gross profit target.

- **Approval workflow**: Routes plan changes through Regional Manager
  and Finance Director review with Microsoft Teams notifications.


In this lab, you are a financial planning analyst at Acme Beverages. You build the planning environment, set the top-down 2026 Sales Target, build the bottom-up 2026 Sales Plan, use the Optimizer to reach the CFO's gross profit goal, and optionally configure the approval workflow.

## Introduction

In this lab, you build a complete planning environment in Microsoft Fabric from scratch. You import the Acme Beverages semantic model, create a Fabric SQL database, set up a Plan (preview) app, and configure semantic model and SQL database connections. You then create a planning sheet, explore the Plan interface, set a top-down 2026 Sales Target, build a bottom-up 2026 Sales Plan, and use the Optimizer to find the mix of revenue growth and cost reduction that meets a gross profit target. An optional final exercise shows how to configure a two-level approval workflow. Planning on a Power BI semantic model, with changes stored in a Fabric SQL database, removes disconnected spreadsheets and manual reconciliation. Built-in tools such as distribution, cell locking, Bulk Edit, comments, approvals, and the Optimizer help you model scenarios quickly and set targets you can defend.

### Objectives

After completing this lab, you will be able to:
- Set up a planning environment by importing the Acme Beverages semantic
  model, creating a Fabric SQL database, and building a Plan app with semantic model and SQL database connections

- Navigate the Plan interface, including the ribbons, header, Explorer
  pane, sidebar panels, and footer

- Create a top-down 2026 Target distributed by prior-year weights and
  apply an H2 skew by locking quarters

- Build a bottom-up 2026 Sales Plan using cell-level adjustments, trend
  distribution, and Bulk Edit uplifts

- Add inline comments, a Comments column, and a Status column to support
  row-level collaboration

- Calculate gross profit with a formula column and run the Optimizer to
  achieve a \$12.5B gross profit target


### Architecture

You import the Acme Beverages .pbix file, which creates a Power BI semantic model and report in a Fabric workspace. The Plan (preview) app connects to the semantic model for governed 2025 actuals and stores plan changes in a Fabric SQL database. Analysts build top-down and bottom-up planning sheets, run the Optimizer to reach the gross profit goal, and route changes through an approval workflow with Microsoft Teams notifications.

![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image1.png)

### Prerequisites

- Access to a Microsoft Fabric workspace that meets the requirements in
  the prerequisites document, with permission to create SQL databases and Plan (preview) items

- The **ACME Beverages.pbix** file downloaded from the lab **Resources**
- Basic familiarity with Power BI semantic models, dimensions, and
  measures

- Access to Microsoft Teams and a second user account (optional, for the
  approval workflow and @mention steps)


## Exercise 1: Set up the planning environment

In this exercise, you build the Acme Beverages planning environment with a workspace, semantic model, SQL database, Plan app, and connections.

>[!Note] When you name the Fabric SQL database, add your name as a suffix, for example Acme_Beverages_JohnS or Acme_Beverages_JS. Fabric adds a unique identifier to connection names automatically. The suffix keeps your items separate in shared workspaces, prevents naming conflicts, and makes your items easier to find and troubleshoot.
>[!Note] When you name the Fabric SQL database, add your name as a
suffix, for example Acme_Beverages_JohnS or Acme_Beverages_JS. Fabric
adds a unique identifier to connection names automatically. The suffix
keeps your items separate in shared workspaces, prevents naming
conflicts, and makes your items easier to find and troubleshoot.

### Task 1: Create your training workspace

In this task, you sign in to Microsoft Fabric and create a workspace on a Fabric capacity. This workspace holds the semantic model, SQL database, and Plan app that you use in the rest of the lab.

1. In a browser, go to the following URL:

    +++https://app.fabric.microsoft.com/+++

1. In the **Email** field, enter +++@lab.CloudPortalCredential(User1).Username+++, and then select **Next**.

    The sign-in page asks for your password.

1. In the **Password** field, enter +++@lab.CloudPortalCredential(User1).Password+++, and then select **Sign in**.

    The Fabric **Home** page opens and shows **Welcome to Fabric**.

1. On the **Home** page, under **Welcome to Fabric**, select **New workspace**.

    The **Create a workspace** pane opens.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image2.png)

1. In the **Name** field, enter the following workspace name:

    `Fabricplan-Day1@lab.LabInstance.Id`

    The message **This name is available** appears under the field.

    >[!Note] The screenshots show the workspace name `Fabricplan-Day176895`. Your workspace name ends with your own lab instance ID.
    >[!Note] The screenshots show the workspace name
    **Fabricplan-Day176895**. Your workspace name ends with your own lab
    instance ID.

1. Select **Advanced** to expand the advanced settings.

    The **Contact list** field shows your user as **(Owner)**, for example **User1-64970352 (Owner)**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image3.png)

1. Under the workspace type options, select **Fabric**.

1. In the **Details** list, select your Fabric capacity, for example **fabriccapacity64970352 - West US 2**.

1. Under **Semantic model storage format**, confirm that **Small semantic model storage format** is selected.

1. Select **Apply**.

    Fabric creates the workspace and opens it.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image4.png)


### Task 2: Create a folder for the lab

In this task, you create a folder in the workspace to hold the items for this lab. The folder keeps your Plan items together and separate from other content in the workspace.

1. In the left navigation pane, select the **Fabricplan-Day1@lab.LabInstance.Id** workspace.

    The workspace opens and shows **Choose from predesigned task flows or add a task to build one**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image5.png)

1. On the workspace toolbar, select **New folder**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image6.png)

    The **New folder** dialog opens.

1. In the **New folder** dialog, enter the following folder name:

    `Day1-Plan`

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image7.png)

1. Select **Create**.

    The **Day1-Plan** folder appears in the workspace item list, with **Type** set to **Folder**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image8.png)


### Task 3: Import the PBIX file

In this task, you import the **ACME Beverages** Power BI file into the **Day1-Plan** folder. The file contains the semantic model and report that the planning exercises use.

1. In the **Fabricplan-Day1@lab.LabInstance.Id** workspace, select the **Day1-Plan** folder.

    The folder opens, and the breadcrumb shows **Fabricplan-Day1@lab.LabInstance.Id** \> **Day1-Plan**.

1. On the workspace toolbar, select **Import** \> **Report, Paginated Report or Workbook** \> **From this computer**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image9.png)

    The **Open** dialog opens.

1. In the **Open** dialog, go to the folder where you saved the file from **Resources**, for example **Local Disk (C:)** \> **Labfiles**.

1. Select the **ACME Beverages** file.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image10.png)

    >[!Note] If you saved the file to another location, such as a OneDrive folder, go to that folder and select the **ACME Beverages** file there.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image11.png)

1. Select **Open**.

    The upload starts, and the **Uploading ACME Beverages.pbix** notification appears in the upper-right corner of the workspace.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image12.png)

    >[!Note] The upload can take a few minutes. Don't select **Cancel upload** while the file uploads.

1. Verify that the **ACME Beverages** report and the **ACME Beverages** semantic model appear in the **Day1-Plan** folder.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image13.png)

    The **Type** column shows **Report** for one item and **Semantic model** for the other.

    >[!Note] A warning such as **Refresh failed due to missing data source credentials** can appear. A message that the data source connection can't be validated can also appear. You can ignore these messages. The imported .pbix file already contains the data, so you can still map rows, columns, and values to your planning sheet.


### Task 4: Create a SQL database

In this task, you create a SQL database in the workspace. The database stores the planning data that you work with in the later exercises.

1. In the **Day1-Plan** folder toolbar, select **+ New item**.

    The **New item** pane opens.

1. In the **New item** pane search box, enter the following search term:

    `sql`

1. Select **SQL database**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image14.png)

    The **New SQL database** dialog opens.

1. In the **Name** field, enter the following name, and then add your name as a suffix, for example Acme_Beverages_JohnS:

    `Acme_Beverages`

    >[!Note] The suffix keeps the database name unique. Use this name wherever the lab refers to your SQL database.

1. Select **Create**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image15.png)

    The database is created and opens. The **Explorer** pane shows your database, and the **Build your database** page appears.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image16.png)

1. In the left navigation pane, select the **Fabricplan-Day1@lab.LabInstance.Id** workspace icon.

    The workspace pane opens. Under **Fabricplan-Day1@lab.LabInstance.Id** \> **Day1-Plan**, your SQL database appears, for example **Acme_Beverages_JohnS**. Its SQL analytics endpoint, which has the same name as your database, is nested under it.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image17.png)

    >[!Note] Fabric creates the SQL analytics endpoint automatically with the SQL database. Your database name includes your own suffix, so it can differ from **Acme_Beverages6857**.

1. In the workspace pane, select **Fabricplan-Day1@lab.LabInstance.Id**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image18.png)

    The **Fabricplan-Day1@lab.LabInstance.Id** workspace opens.

1. In the workspace item list, select the **Day1-Plan** folder.

    The breadcrumb shows **Fabricplan-Day1@lab.LabInstance.Id** \> **Day1-Plan**. The folder contains the following items:
    - **ACME Beverages** report
    - **ACME Beverages** semantic model
    - Your SQL database, for example **Acme_Beverages_JohnS**
    - **Acme_Beverages6857** SQL analytics endpoint

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image19.png)


### Task 5: Create a new Plan app

In this task, you create a Plan app in the **Day1-Plan** folder. The Plan app is where you build the Planning, PowerTable, and Intelligence sheets for the Acme Beverages 2026 plan.

1. In the **Day1-Plan** folder toolbar, select **+ New item**.

    The **New item** pane opens with **All items** selected.

1. In the **New item** pane search box, enter `plan`.

    The **Plan** tile appears with the description **Unify Planning + BI + Data Apps on top of your Semantic Models and Ontologies with writeback**.

1. Select the **Plan** tile.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image20.png)

    The **New Plan** dialog opens.

1. In the **New Plan** dialog, in the **Name** field, enter `Acme_Beverages_Plan`.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image21.png)

1. Confirm that **Location** shows **Fabricplan-Day1@lab.LabInstance.Id** \> **Day1-Plan**.

1. Select **Create**.

    The **Acme_Beverages_Plan** app opens to the **How Would You Like To Get Started?** screen. The **Get data** section lists **Semantic Model** and **Excel / CSV**. The **Create new sheet** section lists **Planning**, **PowerTable**, and **Intelligence**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image22.png)

    >[!Note] The **Home** tab also shows **New Planning Sheet**, **New PowerTable Sheet**, and **New Intelligence Sheet**. The **Explorer (0)** pane shows that the app has no sheets yet.


### Task 6: Connect to the semantic model

In this task, you create a shared cloud connection and use it to connect the Plan app to a semantic model. Planning and BI sheets need this connection to read and use semantic model data.

1. On the **How Would You Like To Get Started?** screen, under **Get data**, select **Semantic Model**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image23.png)

    The **Select Semantic Model Connection** dialog opens.

1. In the **Select Semantic Model Connection** dialog, next to **Select a Connection**, select **+ Create Connection**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image24.png)

    The **Create New Connection** dialog opens.

    >[!Note] The **Connection required** message explains that Planning and BI sheet types need a shared cloud connection to access and use a semantic model.

1. Under **Connection credentials**, confirm that **Connection** shows **Create new connection**.

1. In the **Connection name** field, enter `Acme_Beverages`.

1. Confirm that **Authentication kind** shows **Organizational account**.

1. Under **You are not signed in. Please sign in.**, select **Sign in**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image25.png)

    The Microsoft **Pick an account** window opens.

1. In the **Pick an account** window, select your lab user account, for example `User1-64970352@LODSPRODMCA.onmicrosoft.com`.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image26.png)

    The sign-in window closes, and you're signed in to the connection.

    >[!Note] Your lab user account is +++@lab.CloudPortalCredential(User1).Username+++. The account shows **Signed in** because you already signed in to Microsoft Fabric with it.

1. In the **Create New Connection** dialog, confirm that **You are currently signed in.** appears under **Authentication kind**.

1. Select **Create**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image27.png)

    The connection is created, and the **Create New Connection** dialog closes. The new connection appears as **Acme_Beverages User1-64970352**.

    >[!Note] The number after **User1-** matches your lab user account, **@lab.CloudPortalCredential(User1).Username**. Your connection name might show a different number.

1. In the **Select Semantic Model Connection** dialog, confirm that **Select a Connection** shows **Acme_Beverages User1-64970352**.

1. Under **Select Semantic model From**, confirm that **Semantic Model Item** is selected.

1. Select the **Semantic Model** field.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image28.png)

    The **OneLake catalog** opens on the **Choose a semantic model** page.

1. In the semantic model list, select **ACME Beverages**.

    >[!Note] The **Owner** of the **ACME Beverages** semantic model is your lab user account. Its **Location** is your lab workspace, for example, **Fabricplan-Day1@lab.LabInstance.Id**.

1. Select **Add**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image29.png)

    The **OneLake catalog** closes, and the **Semantic Model** field shows **ACME Beverages**.

1. In the **Select Semantic Model Connection** dialog, confirm that **Select a Connection** shows **Acme_Beverages User1-64970352** and **Semantic Model** shows **ACME Beverages**.

1. Select **Connect**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image30.png)

    The Plan app connects to the **ACME Beverages** semantic model.

1. In the sidebar on the right side of the Plan app, select the **Data** icon.

    The **Data** pane opens.

1. On the **Data** pane, expand **Semantic Model**.

1. Verify that the following tables appear under **Semantic Model**:

    - **Measure Table**
    - **Dim Countries**
    - **Dim Customers**
    - **Dim Date**
    - **Dim Regions**
    - **Dim skus**
    - **Fact Plan Forecast**
    - **Fact Transactions**
    - **P&L Data**

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image31.png)

    The dimension, measure, date, and fact tables from the **ACME Beverages** semantic model are available for planning.


### Task 7: Create a new Planning sheet

In this task, you create a Planning sheet in the Plan app. You use this sheet to build the 2026 sales plan from the **ACME Beverages** semantic model data.

1. In the Plan app, on the **Home** tab, select **New Planning Sheet**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image32.png)

    The **New Planning Sheet** dialog opens.

1. In the **Name** field, enter `Plan Intro`.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image33.png)

1. Select **Create**.

    A new Planning sheet named **Plan Intro** opens.


### Task 8: Assign dimensions to rows

In this task, you add the region and product dimensions to the rows of the Planning sheet. These fields set the levels at which you build and review the 2026 sales plan.

1. On the **Data** pane, expand **Semantic Model** \> **Dim Regions**.

    The **Region Name** field appears under **Dim Regions**.

1. In the sidebar on the right, select the **Fields** icon.

1. Drag **Region Name** into the **Rows** well on the **Fields** pane.

    **Region Name** appears in the **Rows** well.

1. On the **Data** pane, expand **Dim skus**.

    The **Brand**, **Category**, and **SKU Code** fields appear under **Dim skus**.

1. Drag **Category** into the **Rows** well, below **Region Name**.

1. Drag **Brand** into the **Rows** well, below **Category**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image34.png)

    The **Rows** well lists **Region Name**, **Category**, and **Brand**. On the **Data** pane, the **Region Name**, **Brand**, and **Category** check boxes are selected.

    >[!Important] Keep the order **Region Name** → **Category** → **Brand**. Exercise 3 depends on this order (for example, **NA** \> **Beer** \> **Summit Lager**). Some screenshots in this exercise show **Brand** above **Category**; if your **Rows** well shows that order, drag **Category** above **Brand**.

1. Verify that the Planning sheet grid lists each region, starting with **All** and **APAC**, with the product fields nested under each region.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image35.png)

    The status bar shows **Session ready** and the row count for the grid.

    >[!Note] The order of the fields in the **Rows** well sets the nesting in the grid. To change the nesting, drag a field up or down in the **Rows** well. For example, place **Brand** above **Category** to show each category under its brand.


### Task 9: Assign the date hierarchy to columns

In this task, you add the date hierarchy to the columns of the Planning sheet. This lets you plan and compare values by year, quarter, and month.

1. On the **Data** pane, under **Semantic Model**, expand **Dim Date**.

    The **Dim Date** fields appear, including **Date**, **Date Hierarchy**, **Day**, **Month**, **Quarter**, and **Year**.

1. Drag **Date Hierarchy** into the **Columns** well on the **Fields** pane.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image36.png)

    The **Columns** well shows **Date Hierarchy** with the levels **Year**, **Quarter Name**, and **Month Short Name**. On the **Data** pane, the **Date Hierarchy** check box is selected.

1. Verify that the Planning sheet grid shows a **Year** column header above the **Region Name** rows.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image37.png)

    The grid shows the year values, for example **2024**, as column headers. The sheet title reads **by Region Name, Category, Brand, Year, Quarter Name, Month Short Name**.


### Task 10: Assign measures to values

In this task, you add the **2025 Gross Revenue** measure to the values of the Planning sheet. This measure provides the 2025 actuals that the bottom-up plan is built on.

1. On the **Data** pane, under **Semantic Model** \> **Measure Table**, expand the **Revenue** folder.

    The **Revenue** folder lists the measures **2025 Gross Revenue**, **Gross Revenue**, **Revenue PY**, **Revenue YoY %**, **Revenue YTD**, and **Total Net Revenue GC**.

1. Drag **2025 Gross Revenue** into the **Values** well on the **Fields** pane.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image38.png)

    The **Values** well shows **2025 Gross Revenue**. On the **Data** pane, the **2025 Gross Revenue** check box is selected.

1. Verify that the Planning sheet grid shows the **2025 Gross Revenue** column, labeled **in Millions**, under the **2025** year header.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image39.png)

    The grid shows **17,888.87** for **All** and **2,152.03** for **APAC**.


### Task 11: Confirm your matrix is ready

In this task, you confirm that the Planning sheet matrix is complete and adjust the column width for readability. The matrix shows 2025 Gross Revenue by region, brand, and category across the date hierarchy.

1. Verify that the sheet title reads **2025 Gross Revenue by Region Name, Category, Brand, Year, Quarter Name, Month Short Name**.

1. Point to the column header border between **Region Name** and **2025 Gross Revenue** until the sizing handle appears.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image40.png)

1. Drag the sizing handle to adjust the column width.

    The column resizes. The status bar shows **Session ready** with **Rows: 125 Columns: 1**.


### Task 12: Set up a database connection for collaboration

In this task, you create a Fabric SQL connection for the plan. Workspace viewers need this connection to collaborate in Reading view.

1. In the **Extra configuration recommended** banner above the Planning sheet, select **Set up connection**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image41.png)

    The **Select Fabric SQL Connection** dialog opens.

1. In the **Select Fabric SQL Connection** dialog, select **+ Create Connection**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image42.png)

    The **Create New Connection** dialog opens.

1. Under **Connection credentials**, select the **Connection** drop-down.

1. Select **Create new connection**.

1. In the **Connection name** field, enter `Acme_Beverages`.

1. Verify that **Authentication kind** is set to **Organizational account**.

1. Verify that the **You are currently signed in as** section shows your lab user account.

1. Select **Create**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image43.png)

    Fabric creates the connection and returns you to the **Select Fabric SQL Connection** dialog. Fabric adds your user identifier to the connection name.

1. In the **Select a Connection** drop-down, verify that the selected connection name starts with `Acme_Beverages`, followed by your user identifier.

    >[!Note] The user identifier and the number in parentheses after the connection name depend on your lab account. They might differ from the example.

1. Select **Connect**.

    >[!Note] If the dialog asks for a database, select the SQL database that you created in Task 4, for example **Acme_Beverages_JohnS**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image44.png)

    The **Select Fabric SQL Connection** dialog closes, and the plan uses the **Acme_Beverages** connection for viewer collaboration.

1. In the upper-right corner, select the **Notifications** icon.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image45.png)

    The **Notifications** pane opens and confirms that the SQL connection is set up.


## Exercise 2: Explore the interface

In this exercise, you explore the ribbons, header, Explorer pane, sidebar panels, and footer so that you can work confidently before you enter plan data.

### Task 1: Explore the ribbons

In this task, you switch to the full ribbon view and review the commands on each ribbon. Knowing where each command is helps you work faster in the later planning exercises.

1. Select the **Planning** tab.

1. At the right end of the ribbon, select the down arrow to switch to the full ribbon view.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image46.png)

    The ribbon expands and shows labeled command groups, such as **Layout & Number**, **Rows & Columns**, **Tools**, **Actions**, and **Default**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image47.png)

1. Select the **Home** tab.

    The **Home** ribbon contains the following commands:
    - **New Planning Sheet**, **New PowerTable Sheet**, and **New
    Intelligence Sheet**, which you use to add new sheets.

    - **Explore Model**, **Settings**, and **Manage Connections**.


1. Select the **Planning** tab again.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image48.png)

    The **Planning** ribbon contains the following commands:
    - Layout options - **Layout** and **Totals**.
    - Formatting options - **Quick format** and the percentage, currency,
    and decimal buttons.

    - Row and column commands - **Insert Row**, **Manage Rows**,
    **Formula**, **Text**, **List**, **Number**, **Checkbox**, **Date**, **Simulate**, **Person**, **Manage measures**, and **Show Columns**.

    - Tools - **Tools**.
    - Actions - **Audit**, **Notes**, **Writeback**, **Reset**,
    **Export**, and **Undo**.

    >[!Note] In the compact ribbon view, the **Planning** ribbon also shows **Bulk Edit** and **Pivot**.


1. Select the **Model** tab.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image49.png)

    The **Model** ribbon contains commands for forecasts, scenarios, driver-based models, and cubes. The **Rolling Forecast** group contains the following items:
    - Forecast commands - **Forecast**, **Extend Time**, **Reforecast
    Column**, **Reforecast Row**, **Distribute Deficit**, and **Predict**.

    - A date range field that starts with **January 1st, 2024**.
    - The **Show Closed Periods** check box.
    - **Close Period**.

    >[!Note] Until you create a forecast, **Forecast** is the only available command in the **Rolling Forecast** group. The other commands appear dimmed.


1. Select the **Format** tab.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image50.png)

    The **Format** ribbon contains conditional formatting and semantic formatting options. It's organized into the following groups:
    - **Display** - **Appearance**.
    - **Font** - Font name (**Segoe UI**), font size (**13**), increase
    and decrease font size, bold, italic, underline, fill color, font color, and borders.

    - **Alignment** - Text alignment, indent, and row height (**26**)
    options, and **Auto Fit**.

    - **Page Setup** - **Page Break**.


1. Select the **Writeback** tab.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image51.png)

    The **Writeback** ribbon contains commands to write data to destinations, manage settings, and view logs. It's organized into the following groups:
    - **Writeback** - **Writeback**.
    - **Destination** - **Add destination** and **Manage**.
    - **Data** - **WB Type**, **Filter**, and **Mapping**.
    - **Logs** - **Logs**.
    - **General** - **Settings**.

    >[!Note] Writeback**, **Manage**, and **Logs** appear dimmed in the ribbon.


### Task 2: Explore the header, Explorer pane, sidebar, and footer

In this task, you explore the header, the **Explorer** pane, the sidebar, and the footer of a Plan sheet. You review the security, comment, editing, and save commands, and the panels that manage data, fields, filters, comments, bookmarks, and parameters. These controls help you secure the plan and move around it before you enter data.

1. In the header, select **Security**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image52.png)

    The **Security** pane opens and lists the **Data Input**, **Forecast**, **Scenario**, **Comments**, and **Writeback** categories. **Data Input** is selected and shows the message **No data input columns available.**

1. In the **Security** pane, under **Roles**, select **Users**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image53.png)

    The **Users** view opens with the message **No Users Available** and an **Assign Role** button. You use **Assign Role** to add users and map them to roles that manage access and security permissions.

1. Under **Roles**, select **Roles**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image54.png)

    The **Roles (1)** list opens with the **Item Baseline** role, labeled **System Default**. The role settings are on the following tabs:
    - **General**
    - **Planning**
    - **PowerTable**
    - **Intelligence**
    - **Reading View Customization**

    On the **General** tab, under **Select Sheets**, the **Plan Intro** sheet has **Visible** turned on and **Read Only** turned off.

    >[!Note] By default, this role can access all sheets. You can configure individual sheets to restrict access or make them read-only. You can also select **Add Roles** to create more roles.


1. In the **Security** pane, select **Back to Sheet**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image55.png)

    The **Security** pane closes, and the **Plan Intro** sheet opens.

1. In the header, select the **Comments** drop-down arrow.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image56.png)

    The menu opens with **Add new comment**, **View all comments**, and **Settings**. You use **Comments** to add comments to the plan and to view existing comments. **Add new comment** stays unavailable until you save your changes.

1. In the header, select the **Editing** drop-down arrow.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image57.png)

    The menu lists the following views:
    - **Editing** (**Make any changes**) is selected by default.
    - **Reading** (**View the file, but make no changes**) opens the sheet
    in read-only mode.

    You use this menu to switch between the Editing view and the Reading view.


1. In the toolbar, select **Save**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image58.png)

    Your changes to the sheet are saved.

    >[!Note] If the sheet has unsaved changes, pointing to **Comments** shows the message **Please save your changes to use Comments.** Select **Save** before you work with comments.

1. In the toolbar, select the **Save** drop-down arrow.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image59.png)

    The menu opens and shows the **Discard all settings** option. This option removes all unsaved changes and keeps the last saved version of the sheet.

    >[!Important] Don't select **Discard all settings** now. Select it only when you want to remove all unsaved changes.

1. Next to **Explorer (1)**, select the collapse arrow (**«**).

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image60.png)

    The **Explorer** pane collapses, and the sheet canvas gets more space.

1. Select the expand arrow (**»**) to expand the **Explorer** pane again.

    The **Explorer** pane opens. In the **Explorer** pane, you can do the following:
    - Select **+ New Sheet** to add a sheet.
    - Use the **Search** box to find sheets.
    - Select a sheet in the list, for example **Plan Intro**, to open it.


1. In the sidebar on the right, select the **Data** icon.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image61.png)

    The **Data** panel opens. The sidebar has six panels. You use the **Data** panel to manage data from the semantic model, sheets, or queries. The panel has three sections:
    - **Semantic Model** - Lists the model tables **Measure Table**, **Dim
    Countries**, **Dim Customers**, **Dim Date**, **Dim Regions**, **Dim skus**, **Fact Plan Forecast**, **Fact Transactions**, and **P&L Data**.

    - **From Sheets** - Lists data that comes from sheets.
    - **Queries** - Lists data that comes from queries.


1. In the sidebar, select the **Fields** icon.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image62.png)

    The **Fields** panel opens. You use this panel to map dimensions and measures to rows, columns, and values. The current sheet has the following mapping:
    - **Rows** - **Region Name**, **Category**, and **Brand**. Select **+
    Add Rows** to add more fields.

    - **Columns** - **Date Hierarchy** with **Year**, **Quarter Name**,
    and **Month Short Name**. Select **+ Add Columns** to add more fields.

    - **Values** - **2025 Gross Revenue**.


1. In the sidebar, select the **Filter** icon.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image63.png)

    The **Filter** panel opens. You use this panel to filter the data in the sheet. The **Page level** section lists a filter card for each field in the sheet. Each card is set to **is (All)**:
    - **Region Name**
    - **Category**
    - **Brand**
    - **Year**
    - **Quarter Name**
    - **Month Short Name**
    - **2025 Gross Revenue**

    You can drag more fields to **Add data fields here**. The **Global level** section holds filters that apply beyond the current page.


1. In the sidebar, select the **Comments** icon.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image64.png)

    The **All Comments** panel opens. You use this panel to view and manage comments on the sheet. The panel has the following controls:
    - **Search** - Finds comments by text.
    - Filter drop-down - Filters the list of comments.

    The panel shows **No comments found** because no annotations exist yet. After you add a comment, it appears in this panel.


1. In the sidebar, select the **Bookmark** icon.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image65.png)

    The **Bookmark** panel opens. You use this panel to manage bookmarks on the **Public** and **Private** tabs. You can select **Add New** to create a bookmark. The panel shows **No bookmarks available.** because no bookmarks exist yet.

1. In the sidebar, select the **Parameter** icon (**{x}**).

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image66.png)

    The **Parameter** panel opens. You use this panel to add parameters on the **Page Level** and **Global Level** tabs. You can select **+ Add new** to create a parameter. The panel shows **No Parameters Found** because no parameters exist yet.

1. In the footer of the sheet, select **Rows: 125 Columns: 1**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image67.png)

    The statistics for the dimensions and measures in the sheet appear. The sheet **2025 Gross Revenue by Region Name, Category, Brand, Year, Quarter Name, Month Short Name** has 125 rows and 1 column. The footer also shows **Session ready** and the **Sync** option.

1. In the footer, use the zoom slider or the **−** and **+** icons to adjust the canvas size.

    The canvas zooms in or out, and the zoom box shows the current zoom level, for example **100 %**.

1. In the footer, select the **Settings** icon.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image68.png)

    The **Settings** options for the sheet open.


## Exercise 3: Top-down and bottom-up planning

In this exercise, you set a top-down 2026 target, build a bottom-up 2026 sales plan from prior-year actuals, and add inline comments and a **Status** column for row-level collaboration.

### Task 1: Expand the column hierarchy and enable the column grand total

In this task, you expand the **Year** column to show quarterly values and add a column grand total on the left of the sheet. Quarterly columns let you review and distribute the plan by period. The grand total shows the full-year value for each row.

1. On the **Plan Intro** sheet, in the **Year** column header, select the expand icon (**\>**) next to **2025**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image69.png)

    The **Year** column expands to show the quarters.

1. Review the **Quarter Name** row, which shows **Q1**, **Q2**, **Q3**, and **Q4** under **2025**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image70.png)

    The sheet shows **2025 Gross Revenue** by quarter, for example **4,473.63** for **All** in **Q1**.

1. On the ribbon, select the **Planning** tab.

1. On the **Planning** tab, select **Totals**.

    The **Totals** menu opens with the **Row Grand Total**, **Column Grand Total**, **Row Subtotal**, and **Column Subtotal** options.

1. In the **Column Grand Total** list, which is set to **Off**, select **Left**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image71.png)

    A **Grand Total** column appears on the left of the quarterly columns.

1. Review the **Grand Total** column on the left of the sheet, which shows the full-year **2025 Gross Revenue** values next to the quarterly values.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image72.png)

    The **Grand Total** column shows the full-year 2025 actuals, for example **17,888.87** for **All** and **2,152.03** for **APAC**.


### Task 2: Insert the 2026 Target input column

In this task, you create a **2026 Target** input series by copying the **2025 Gross Revenue** series. The new column starts with the 2025 actuals. This gives Finance a baseline to adjust when setting the top-down revenue target.

1. On the **Planning** tab, select **Number** \> **Copy from another series** \> **2025 Gross Revenue**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image73.png)

    The **Data Input** pane opens.

1. In the **Title** field, enter the following value:

    `2026 Target`

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image74.png)

1. Verify that the following fields keep their default values:

    - Insert as - **Visual Measure**
    - Input type - `Number`
    - Column aggregation type - `Sum`


1. Select **Create**.

    A **2026 Target** column appears next to each **2025 Gross Revenue** column.

1. Review the **2026 Target** values under **Grand Total** and under each quarter (**Q1**, **Q2**, **Q3**, and **Q4**).

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image75.png)

    The **2026 Target** values match the **2025 Gross Revenue** values. For example, the **Grand Total** shows **17,888.87** for **All**, and **Q4** shows **4,466.25** for **All**.


### Task 3: Enter the top-level target

In this task, you enter the full-year 2026 revenue target in the **Grand Total** cell of the **2026 Target** column. Plan distributes the value to every region, category, and brand based on the 2025 gross revenue mix. This gives Finance a top-down starting point.

1. In the **Grand Total** column, double-click the **2026 Target** cell in the **All** row.

    The cell shows **17,888.87**, and the formula bar becomes active.

1. In the formula bar, enter the following value:

    `18.5b`

    The formula bar shows **18,500,000,000** as a preview of the value.

1. Select the check mark icon to the right of the formula bar.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image76.png)

    The **2026 Target** cell for **All** in the **Grand Total** column shows **18,500.00**.

1. Review the **2026 Target** values for the regions, brands, and quarters.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image77.png)

    The \$18.5B target is distributed proportionally to all regions, categories, and brands based on the 2025 gross revenue mix. Regions and brands with higher 2025 actuals receive a larger share of the target. For example, **APAC** shows **2,225.55**, and **Q1** shows **4,626.46** for **All**.


### Task 4: Apply an H2 skew

In this task, you lock the **Q1** and **Q2** targets so that later changes to the full-year target flow only to the second half of the year. This lets Finance weight growth toward H2 without changing the first-half values.

1. In the **Q1** column, select the **2026 Target** cell in the **All** row.

    The cell shows **4,626.46**, and the distribution icon appears to the right of the cell.

1. Select the distribution icon next to the selected cell.

    A menu opens with distribution and lock options.

1. Select **Lock all children**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image78.png)

    The **Q1** **2026 Target** cell and all cells below it are locked.

1. In the **Q2** column, select the **2026 Target** cell in the **All** row.

    The cell shows **4,635.68**.

1. Select the distribution icon next to the selected cell.

1. Select **Lock all children**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image79.png)

    The **Q2** **2026 Target** cell and all cells below it are locked. The locked **Q1** and **Q2** **2026 Target** columns appear shaded.

    >[!Note] When you enter a new value in the **Grand Total** cell, Plan skips the locked quarters. It distributes the value only to the unlocked periods, **Q3** and **Q4**.

1. In the **Grand Total** column, select the **2026 Target** cell in the **All** row.

    The cell shows **18,500.00**, and its value appears in the formula bar above the grid.

1. In the formula bar, replace the current value with the following value:

    `18.75b`

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image80.png)

    A preview below the formula bar shows **18,750,000,000**.

1. Press **Enter**, or select the check mark (**✓**) to the right of the formula bar.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image81.png)

    The **Grand Total** **2026 Target** cell in the **All** row shows **18,750.00**. Plan distributes the additional \$0.25B only across **Q3** and **Q4**. The locked **Q1** value stays at **4,626.46**, and the locked **Q2** value stays at **4,635.68**.

1. In the **Q1** column, select the **2026 Target** cell in the **All** row.

    The cell shows **4,626.46**, and the distribution icon appears to the right of the cell.

1. Select the distribution icon next to the selected cell.

    A menu opens with the **Unlock this cell** and **Unlock all children** options.

1. Select **Unlock all children**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image82.png)

    The **Q1** **2026 Target** cell and all cells below it are unlocked.

1. In the **Q2** column, select the **2026 Target** cell in the **All** row.

    The cell shows **4,635.68**.

1. Select the distribution icon next to the selected cell.

1. Select **Unlock all children**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image83.png)

    The **Q2** **2026 Target** cell and all cells below it are unlocked, and the **Q1** and **Q2** columns are no longer shaded. The **2026 Target** values in the **All** row keep the H2 skew. **Q1** shows **4,626.46**, **Q2** shows **4,635.68**, **Q3** shows **4,744.03**, and **Q4** shows **4,743.83**. The **Grand Total** **2026 Target** cell in the **All** row remains **18,750.00**.

    >[!Note] In practice, teams typically do top-down and bottom-up planning on separate sheets at different levels of granularity. Finance works from the total down, and Sales builds from the detail up. This lab shows both on the same sheet to keep the setup focused on the mechanics.


### Task 5: Insert the 2026 Sales Plan input column

In this task, you create a **2026 Sales Plan** input column by copying the **2025 Gross Revenue** series. This column gives Sales a bottom-up starting point that is pre-populated with 2025 actuals. You adjust these values in later tasks.

1. On the **Planning** tab, in the **Rows & Columns** group, select **Number**.

    A menu opens with the **Insert a new empty series** and **Copy from another series** options.

1. Select **Copy from another series** \> **2025 Gross Revenue**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image84.png)

    The **Data Input** pane opens on the right side of the sheet.

1. In the **Title** field, enter the following value:

    `2026 Sales Plan`

1. Confirm that the remaining fields in the **Data Input** pane are set as follows:

    - Insert as - **Visual Measure**
    - Input type - `Number`
    - Column aggregation type - `Sum`
    - Row aggregation type - `Sum`
    - Distribute parent value to children - selected


1. Select **Create**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image85.png)

    The **2026 Sales Plan** column appears next to **2026 Target** in each period. The column is pre-populated with the 2025 values. For example, the **Grand Total** **2026 Sales Plan** cell in the **All** row shows **17,888.87**, and the **Q1** cell in the **All** row shows **4,473.63**. The counter on **Manage measures** on the ribbon changes to **2**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image86.png)


### Task 6: Adjust the Summit Lager plan in North America

In this task, you show all rows on one page and collapse the row hierarchy to the region level. You then raise the **2026 Sales Plan** value for **Summit Lager** in North America and distribute it across the quarters with a growth trend. This shows how one change at the top level rolls up through the hierarchy and spreads to the quarterly values.

1. In the status bar in the lower-right corner of the sheet, select the **Settings** (gear) icon.

    The **Settings** pane opens with the **Panel Aggregation** and **Rows Per Page** fields.

1. Select the **Rows Per Page** list, which is set to **Auto**.

1. Select **All**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image87.png)

    All rows of the sheet appear on a single page.

1. Point to the **APAC** row, and then select the row gripper icon that appears to the left of it.

    A menu opens with the **Select**, **Expand**, **Collapse**, **Insert**, **Actions**, and **Audit** options.

1. Select **Collapse** \> **Specific Level** \> **Region Name**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image88.png)

    The row hierarchy collapses to the region level. The sheet shows only the **All**, **APAC**, **EU**, **LA**, and **NA** rows.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image89.png)

1. Select the expand icon (**⊞**) next to **NA**.

    The **NA** row expands and shows the **Beer**, **Hard Seltzer**, and **Non-Alcoholic** category rows.

    >[!Note] The category rows appear under **NA** when **Category** is above **Brand** in the **Rows** well of the **Fields** pane. In that case, the sheet title reads **...by Region Name, Category, Brand...**. If brands appear under **NA** instead, check the order of the fields in the **Rows** well.

1. Select the expand icon (**⊞**) next to **Beer**.

    The **Beer** row expands and shows its brand rows, including **Artisan Ale**, **Cosmic Craft**, **Eclipse Light**, **Old Mill**, **Riverbend**, and **Summit Lager**. In the **Grand Total** columns, the **Summit Lager** row shows **4,395.74** for **2025 Gross Revenue** and **4,607.33** for **2026 Target**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image90.png)

1. In the **Summit Lager** row, double-click the **Grand Total** cell in the **2026 Sales Plan** column.

    The cell switches to edit mode. Before the change, the cell shows **4,395.74**. The formula bar shows the existing value **4395741014.28001**.

1. In the formula bar, place the cursor at the end of the existing value.

1. Type **+8%** (a plus sign followed by 8%), so that the formula bar shows the existing value followed by **+8%**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image91.png)

    A preview below the formula bar shows the new value **4,747,400,295.422**.

1. Press **Enter**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image92.png)

    The **Values saved** notification appears. In the **Summit Lager** row, the **Grand Total** for **2026 Sales Plan** changes to **4,747.40**. The change rolls up through the hierarchy:
    - **Beer** changes to **8,386.03**.
    - **NA** changes to **9,572.22**.
    - **All** changes to **18,240.53**.


1. In the **Summit Lager** row, select the **Grand Total** cell in the **2026 Sales Plan** column.

    The distribution icon appears next to the cell.

1. Select the distribution icon (**⌄**) next to the cell.

    A menu opens with the following options:
    - **Distribute equally**
    - **Distribute to column with trend**
    - **Distribute to columns by weights of 2025 Gross Revenue**
    - **Distribute to columns by weights of 2026 Target**
    - **Distribute to columns by weights of 2026 Sales Plan**
    - **Lock this cell**
    - **Lock all children**


1. Select **Distribute to column with trend**.

    A slider and the **Input custom value** option appear.

1. Drag the slider to **4%**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image93.png)

    The adjusted annual total of **4,747.40** is distributed across the quarters with a 4% growth trend.

1. Scroll right to review the quarterly **2026 Sales Plan** values in the **Summit Lager** row.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image94.png)

    The quarterly values increase from quarter to quarter:
    - **Q1** - **986.27**
    - **Q2** - **1,109.42**
    - **Q3** - **1,247.94**
    - **Q4** - **1,403.77**

    **Q1** receives the smallest share of the annual total, and **Q4** receives the largest.


### Task 7: Apply a summer Q3 uplift across key markets

In this task, you use **Bulk Edit** to apply an 8% uplift to the **2026 Sales Plan** for Beer and Hard Seltzer in the EU and Latin America during Q3. This applies the summer promotion to the whole segment in one action, so you don't need to edit each cell.

1. On the ribbon, select **Planning** \> **Bulk Edit**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image95.png)

    The **Bulk Edit** dialog opens.

1. In the **Measure** list, select **2026 Sales Plan**.

1. Under **Select Filter**, add the following filters by selecting **+ Add Filter** for each one:

    - Region Name - `Is` - **EU**, **LA**
    - Category - **Is** - **Beer**, **Hard Seltzer**
    - Quarter Name - `Is` - **Q3**


1. Enter the following details for the remaining fields:

    - Apply to Row Level - **Brand**
    - Apply to Column Level - **Month Short Name**
    - Type - **Append By**
    - Value - `8%`
    - Distribution - **Distribute by weights of 2026 Sales Plan**

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image96.png)

    >[!Note] The **Distribution** field defaults to **Distribute by weights of 2026 Sales Plan**.


1. Select **Apply**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image97.png)

    The **Values saved** notification appears, and the updated cells are highlighted. The **2026 Sales Plan** totals change as follows:
    - **All** - **18,362.79**
    - **EU** - **4,539.45**
    - **LA** - **2,099.09**


1. Review the **Q3** values in the **2026 Sales Plan** column for **EU** and **LA**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image98.png)

    The **Q3** **2026 Sales Plan** value for **EU** is **1,195.33**, and the value for **LA** is **551.20**. The **Q3** total for **All** is **4,747.00**.

1. Verify that the **Q3** **2026 Sales Plan** values for **Beer** and **Hard Seltzer** in **EU** and **LA** increased by 8%, and that the rest of the plan is unchanged.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image99.png)

    The highlighted cells show the updated values:
    - **All** **Grand Total** - **18,362.79**
    - **EU** **Grand Total** - **4,539.45**
    - **LA** **Grand Total** - **2,099.09**
    - **All** **Q3** total - **4,747.00**
    - **EU** **Q3** - **1,195.33**
    - **LA** **Q3** - **551.20**

    The values for **APAC** and **NA** don't change.


### Task 8: Add inline comments

In this task, you add an inline comment to the Summit Lager plan value to record why the number changed. Inline comments keep the reasoning behind plan changes next to the data, so reviewers and approvers can understand each adjustment.

1. In the grid, under **NA** \> **Beer**, select the **Grand Total** cell in the **2026 Sales Plan** column for **Summit Lager** (**4,747.40**).

1. On the toolbar in the upper-right corner, select **Comments**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image100.png)

    A comment box opens next to the selected cell.

1. In the comment text box, enter the following text:

    `8% uplift agreed with the NA sales lead. Annual total distributed across quarters with a 4% growth trend`

1. Select the **Send** (arrow) icon.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image101.png)

    The **Comment added** notification appears. The **Summit Lager** cell shows a green comment indicator in its corner.

1. Select the comment indicator on the **Summit Lager** **2026 Sales Plan** cell.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image102.png)

    The comment opens and shows your comment text, the date and time that you added it, and a **Reply** box.

    >[!Note] The comment card shows the author, the date and time, and the comment text. Use the **Reply** box to reply to the comment. Use the **Edit** (pencil) and **Delete** (trash) icons to change or remove the comment.

1. In the comment card, select the **More options** (**...**) icon.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image103.png)

    A menu opens with the **Resolve Thread** and **Lock Thread** actions. The menu also has a **Sort by** section with the **Newest to Oldest** and **Oldest to Newest** options. Use these options to close a discussion, prevent further replies, or change the order of replies in the thread.


### Task 9: Enable the comments column

In this task, you turn on the **Comments** column so that comments appear in a dedicated column in the grid. Reviewers can then read the reasoning for each row next to the plan numbers without opening each comment indicator.

1. On the toolbar in the upper-right corner, select the arrow next to **Comments**.

1. Select **Settings**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image104.png)

    The **Comment Settings** dialog opens.

1. Under **Comments Column**, set the toggle to `Show`.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image105.png)

    >[!Note] Leave the other settings at their current values. For example, leave **Enable Commenting** set to **On** and **Teams Notification** set to **On**.

1. Select **Save**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image106.png)

    The **Comment Settings** dialog closes. A **Comments** column appears on the right side of the grid.

1. In the **Comments** column, double-click the cell for the **NA** row.

    A comment card opens with an **Assign to user** option and a comment text box.

1. In the comment text box, enter the following text:

    `Sales Plan reviewed and ready for submission`

1. In the same text box, enter the following character to tag another user:

    `@`

    A list of users appears.

1. In the list, select the user to tag, for example **Accounting-User-*{ID}***.

    The tagged user's name appears as a link in the comment text.

1. In the lower-right corner of the comment card, select **Post** (send arrow).

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image107.png)

    A **Comment added successfully** notification appears in the upper-right corner. The comment appears in the **Comments** column for the **NA** row. The comment card shows your user name, the date and time, the comment text, and the tagged user.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image108.png)


### Task 10: Add a status column

In this task, you collapse the column hierarchy and add a **Status** list column to the grid. Collaboration columns let reviewers record structured inputs, such as a status, next to the plan numbers for each row.

1. In the column header area, select the collapse arrow (**˅**) next to **2025** in the **Year** row.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image109.png)

    The quarter columns collapse, and the grid shows the year-level totals.

1. On the **Planning** tab, in the **Rows & Columns** group, select **List** \> **Single Select**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image110.png)

1. In the **Data Input** pane, in the **Title** field, enter the following value:

    `Status`

    **Insert as** remains set to **Visual Column**, and **Input type** remains set to **Single Select**.

1. Under **Options**, select **Presets**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image111.png)

    The **Add options from Preset** dialog opens. It lists preset option sets, such as **Yes/No**, **Up/Down**, **Open/Closed**, **Project Status**, **Approval Status**, and **Process Status**.

1. In the **Add options from Preset** dialog, select **Process Status**.

1. Select **Apply**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image112.png)

    The dialog closes. Under **Options**, the **Data Input** pane lists the preset values **Not Started**, **Started**, **In Progress**, **In Review**, and **Completed**. Each value has its own color.

1. Select **Create**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image113.png)

    A **Measure created successfully** notification appears in the upper-right corner. The new **Status** column appears in the grid, to the left of the **Comments** column.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image114.png)

1. In the grid, in the **NA** row, select the **Status** cell.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image115.png)

    A list opens. It contains a **Search for an option** box and the options **Not Started**, **Started**, **In Progress**, **In Review**, and **Completed**.

1. In the list, select **In Progress**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image116.png)

    The **Status** cell in the **NA** row shows **In Progress** with a yellow highlight.

1. In the upper-right corner of the ribbon, select **Save**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image117.png)

    A **Report saved successfully!** notification appears in the upper-right corner.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image118.png)


## Exercise 4: Optimizer

In this exercise, you combine the 2026 Sales Plan with 2025 COGS in a planning sheet and use the Optimizer to reach a \$12.5B gross profit target.

### Task 1: Set up the Gross Profit sheet

In this task, you create a planning sheet named Gross Profit and assign its rows, columns, and values. This sheet brings revenue and cost data together so that the Optimizer can calculate gross profit by region, category, and brand.

1. On the **Home** tab, in the **Create** group, select **New Planning Sheet**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image119.png)

    The **New Planning Sheet** dialog opens.

1. In the **Name** field, enter the following value:

    `Gross Profit`

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image120.png)

1. Select **Create**.

    The new **Gross Profit** sheet opens with the **Fields** and **Data** panes on the right.

1. In the **Data** pane, under **Semantic Model** \> **Dim Regions**, select the **Region Name** check box.

    **Region Name** appears in the **Rows** well of the **Fields** pane.

1. Under **Semantic Model** \> **Dim skus**, select the **Category** check box.

1. Under **Semantic Model** \> **Dim skus**, select the **Brand** check box.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image121.png)

    **Category** and **Brand** appear in the **Rows** well under **Region Name**.

    >[!Note] The rows must be in the order **Region Name**, **Category**, **Brand**. If **Brand** appears above **Category** in the **Rows** well, drag **Category** above **Brand**.

1. Under **Semantic Model** \> **Dim Date**, select the **Date Hierarchy** check box.

    **Date Hierarchy** appears in the **Columns** well with the levels **Year**, **Quarter Name**, and **Month Short Name**.

1. In the **Data** pane, under **From Sheets** \> **Plan Intro**, select the **2026 Sales Plan** check box.

    **Sum of 2026 Sales Plan** appears in the **Values** well.

1. Under **Semantic Model** \> **Measure Table** \> **Cost & Discounts**, select the **2025 COGS** check box.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image122.png)

    **2025 COGS** appears in the **Values** well under **Sum of 2026 Sales Plan**.

1. In the **Values** well, double-click the **Sum of 2026 Sales Plan** label.

    The label becomes editable.

1. Enter the following name:

    `2026 Sales Plan`

    The label in the **Values** well changes to **2026 Sales Plan**.

1. In the **Data** pane, verify that the following check boxes are selected:

    - **Semantic Model** \> **Measure Table** \> **Cost & Discounts** \>
    **2025 COGS**

    - **From Sheets** \> **Plan Intro** \> **2026 Sales Plan**

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image123.png)

    Both fields appear in the **Values** well.


1. In the **Values** well, drag **2025 COGS** above the **2026 Sales Plan** field.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image124.png)

    **2025 COGS** moves to the first position in the **Values** well.

1. Verify that the **Values** well shows **2025 COGS** first and **2026 Sales Plan** second.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image125.png)

    >[!Note] If the label still reads **Sum of 2026 Sales Plan**, repeat steps 10 and 11 to rename it to **2026 Sales Plan**.


### Task 2: Create data input columns for the Optimizer

In this task, you create editable data input columns by copying the existing **2026 Sales Plan** and **2025 COGS** series. The Optimizer adjusts these input columns to calculate the revenue growth and cost reduction needed to reach the gross profit goal.

1. On the **Planning** tab, select **Number** \> **Copy from another series** \> **2026 Sales Plan**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image126.png)

    The **Save changes to proceed** dialog box opens.

1. In the **Save changes to proceed** dialog, select **Save and continue**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image127.png)

    The changes are saved, and the **Data Input** pane opens.

1. In the **Data Input** pane, in the **Title** field, enter the following value:

    `Sales Plan`

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image128.png)

1. Verify that the **Data Input** pane shows the following settings:

    - Insert as - **Visual Measure**
    - Input type - `Number`
    - Column aggregation type - `Sum`
    - Row aggregation type - `Sum`
    - Distribute parent value to children - **Selected**


1. Select **Create**.

    The **Sales Plan** column is added to the table. It shows the same values as **2026 Sales Plan**, for example **18,362.79** for **All**.

1. On the **Planning** tab, select **Number** \> **Copy from another series** \> **2025 COGS**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image129.png)

1. In the **Data Input** pane, in the **Title** field, enter the following value:

    `COGS`

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image130.png)

1. Verify that the **Data Input** pane shows the following settings:

    - Insert as - **Visual Measure**
    - Input type - `Number`
    - Column aggregation type - `Sum`
    - Row aggregation type - `Sum`
    - Distribute parent value to children - **Selected**


1. Select **Create**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image131.png)

    The **COGS** column is added to the table. It shows the same values as **2025 COGS**, for example **7,543.23** for **All**.

    >[!Note] The **Sales Plan** and **COGS** columns are editable copies, and the Optimizer updates only these copies. The original **2026 Sales Plan** series from **From Sheets** and the **2025 COGS** semantic model measure stay unchanged. You hide the originals now to keep the grid clean. After the Optimizer runs, you show them again to compare the results.

1. On the **Planning** ribbon, select **Show Columns**.

    The **Show Columns** pane opens.

1. In the **Show Columns** pane, clear the **2025 COGS** and **2026 Sales Plan** check boxes.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image132.png)

    The grid shows only the **Sales Plan** and **COGS** columns.


### Task 3: Calculate gross profit

In this task, you add a formula column that calculates gross profit from the editable **Sales Plan** and **COGS** columns. The Optimizer uses this gross profit value to check progress toward the \$12.5B goal.

1. On the **Planning** ribbon, in the **Rows & Columns** group, select **Formula**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image133.png)

    The **Formula Measure** pane opens.

1. In the **Formula Measure** pane, enter the following details:

    - Title - `Gross Profit`
    - Insert as - **Visual Measure**
    - Data type - `Number`
    - Formula - `[Sales Plan]-[COGS]`
    - Column aggregation type - `Formula`
    - Row aggregation type - `Formula`

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image134.png)

    >[!Note] Make sure the formula references the editable **Sales Plan** and **COGS** input columns that you created in the previous task. Don't select the original **2026 Sales Plan** or **2025 COGS** columns.


1. Select **Create**.

    The **Gross Profit** column is added to the grid. It's calculated from the **Sales Plan** and **COGS** columns. For example, the **All** row shows **10,819.56**. When you select a **Gross Profit** cell, the formula bar shows **= \[Sales Plan\]-\[COGS\]**.

1. Review the grid. Each region, brand, and category row shows the **Sales Plan**, **COGS**, and **Gross Profit** columns, and the **All** row shows **18,362.79**, **7,543.23**, and **10,819.56**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image135.png)


### Task 4: Run the Optimizer

In this task, you start the Optimizer and set the CFO's \$12.5B gross profit goal as the target. The Optimizer adjusts the **Sales Plan** and **COGS** columns to find a combination that reaches this goal.

1. In the **Gross Profit** column, select the grand total cell in the **All** row.

    The **Optimize** option on the **Planning** ribbon becomes active.

1. On the **Planning** ribbon, in the **Tools** group, select **Optimize**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image136.png)

    The **Objective & Variables** dialog opens. It shows **GrandTotal** and **2025 \> Gross Profit** as the cell to optimize.

1. In the **Objective & Variables** dialog, enter the following details:

    - Objective - **Target**
    - Target Value - `12.5b`
    - Variables to Update - **Sales Plan**, **COGS**

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image137.png)


1. Select **Next**.

    The **Add Constraints** page opens and shows **No Constraints Added**.

1. On the **Add Constraints** page, select **Run**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image138.png)

    The Optimizer runs without constraints, and the **Output** page opens.

1. Under **Results**, confirm that **Target Value** shows **12.50b** and **Achieved** shows **12.50b** with a green check mark.

1. Under **Variables**, review the Optimizer's solution for **Sales Plan** and **COGS**.

    For example, **Sales Plan** shows **19.20b** and **COGS** shows **6.70b**. (The screenshot is from a different run and shows **19.87b** and **7,371.50m**.)

    >[!Note] The Optimizer can return different **Sales Plan** and **COGS** values on each run. Your values can differ from the examples in this lab. **Achieved** must still match the **Target Value** of **12.50b**.

1. Select **Apply**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image139.png)

    The Optimizer writes the optimized **Sales Plan** and **COGS** values to the sheet. The **Gross Profit** grand total in the **All** row shows **12,500.00**.

1. In the grid, select the expand icon (**⊞**) next to **APAC**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image140.png)

    Each brand and category shows updated **Sales Plan**, **COGS**, and **Gross Profit** values. For example, **Hard Seltzer** \> **AquaSplash** shows **35.97**, **11.64**, and **24.34**.

1. On the **Planning** ribbon, select **Show Columns**.

    The **Show Columns** pane opens and lists the fields in the sheet.

1. In the **Show Columns** pane, select the **2025 COGS** and **2026 Sales Plan** check boxes.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image141.png)

    The **2025 COGS** and **2026 Sales Plan** columns appear next to the optimized **Sales Plan** and **COGS** columns.

1. In the **All** row, compare the **Sales Plan** value with the original **2026 Sales Plan** value.

    For example, **Sales Plan** shows **19,203.01** and **2026 Sales Plan** shows **18,362.79**. This is an increase of about 840 million.

1. In the **All** row, compare the **COGS** value with the original **2025 COGS** value.

    For example, **COGS** shows **6,703.01** and **2025 COGS** shows **7,543.23**. This is a reduction of about 840 million.

    The revenue increase and the cost reduction together deliver the **Gross Profit** target of **12,500.00** million (\$12.5B).


## Exercise 5: Approval workflow

In this exercise, you set up a two-level approval workflow (**Regional Manager** and **Finance Director**) on the **Plan Intro** sheet and verify it with a submitted change.

>[!Note] This exercise is optional. Approvers must be users in your organization. In a single-user lab, you can select your own account for both levels.

### Task 1: Enable the approval workflow

In this task, you set up two approval levels so that plan changes go to the regional manager first and then to the finance director. This ensures that someone reviews every change to the 2026 plan before the plan is final.

1. In the **Explorer** pane, select the **Plan Intro** sheet.

1. On the ribbon, select the **Model** tab.

1. On the **Model** tab, in the **Workflows** group, select **Approval**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image142.png)

    The **Approval Workflow** pane opens on the **Who needs to approve your data?** page. It shows two level cards named `Level` and **Level (2)**.

1. In the first level card, select the edit (pencil) icon next to **Level**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image143.png)

1. In the level name field, enter the following name:

    `Regional Manager`

1. Under **Select Approvers By**, confirm that **Email Address** is selected.

1. Select the **Select users...** field.

1. Enter the name or email address of the user who acts as the regional manager, for example the second lab user provided in your lab environment.

1. Select the regional manager's account from the list.

    The user appears in the **Select users...** field of the **Regional Manager** level.

1. In the second level card, select the edit (pencil) icon next to **Level (2)**.

1. In the level name field, enter the following name:

    `Finance Director`

1. Under **Select Approvers By**, confirm that **Email Address** is selected.

1. Select the **Select users...** field.

1. Enter the name or email address of the user who acts as the finance director, for example your own lab user account.

1. Select the finance director's account from the list.

    The user appears in the **Select users...** field of the **Finance Director** level.

1. Confirm that the **Allow entry on Totals/Subtotals** check box is selected for both levels.

1. Select **Next**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image144.png)

    The **How should the approval flow work?** page opens.

1. Under **Reset on Rejection**, turn on the **Reset all previous levels to In Progress** toggle.

    When an approver rejects a change, all previous approval levels return to **In Progress** for editing.

1. Under **Teams Notifications**, confirm that the **When submitted For review** and **When approved/rejected** toggles are turned on.

1. Select **Submit**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image145.png)

    The approval workflow for the **Plan Intro** sheet starts saving with the **Regional Manager** and **Finance Director** levels.

1. Wait for the approval workflow to finish saving.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image146.png)

    The **How should the approval flow work?** page closes, and the **Plan Intro** sheet opens again.

1. In the sheet, scroll right to the **Status** column at the end of the columns.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image147.png)

    The **Status** column shows **In Progress** for the **NA** row. The **Regional Manager** and **Finance Director** approval columns appear next to the **Status** column.


### Task 2: Verify the approval workflow

In this task, you change a value in the 2026 Sales Plan and submit the change for approval. This confirms that plan changes go through the approval workflow before the plan is finalized.

1. In the sheet, under **Region Name**, select the expand icon (**⊞**) next to **APAC**.

    The **APAC** row expands and shows its product categories, including **Beer**, **Hard Seltzer**, and **Non-Alcoholic**.

1. In the **APAC** \> **Beer** row, double-click the cell under **Grand Total** \> **2026 Sales Plan**.

    The formula bar opens and shows the current value of the cell, **1894286222.9**.

1. In the formula bar, place the cursor after the existing value.

1. Type **+10%** (a plus sign followed by 10%) to increase the plan by 10 percent.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image148.png)

    A preview of the new value, **2,083,714,845.19**, appears below the formula bar.

1. Press **Enter**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image149.png)

    The **Beer** value in the **2026 Sales Plan** column changes to **2,083.71**. The **APAC** total changes to **2,341.46**. The quarterly **2026 Sales Plan** values for **APAC** and **Beer** also update.

1. In the sheet, under **Regional Manager**, select the cell in the **APAC** \> **Beer** row.

    A list opens. It shows the **Search for an option** box and the **In Progress**, **Submitted**, and **Reopened** options.

1. In the list, select **Submitted**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image150.png)

    The **Regional Manager** cell in the **Beer** row shows **Submitted**.

1. Verify that the **Finance Director** cell in the **Beer** row shows **Pending**.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image151.png)

    The **Pending** status confirms that the submission is routed to the finance director for approval.

    >[!Note] When the regional manager submits the change, Microsoft Teams sends a notification to the finance director. The finance director can then approve or reject the change.

1. In the sheet, under **Finance Director**, select the **Pending** cell in the **APAC** \> **Beer** row.

    ![Screenshot](https://raw.githubusercontent.com/technofocus-pte/entrprsplnfrcIQdepth/refs/heads/main/Cloudslice/Labguides/Lab%2001/media/image152.png)

    A list opens. It shows **Pending** as the current value, the **Search for an option** box, and the **Approved** and **Rejected** options. The finance director selects one of these options to approve or reject the change.

1. Press **Esc** to close the list without changing the status.

    The list closes, and the **Finance Director** cell in the **Beer** row still shows **Pending**.


### Outcomes

This section recaps what you accomplished in this lab. Use it to confirm that you completed each exercise before you read the summary.

    - You set up the training environment. You imported the semantic model,
  created the SQL database, built the Plan app, and configured both connections.

    - You created the **Plan Intro** sheet. The sheet has **Region Name** \>
  **Category** \> **Brand** rows, date hierarchy columns, and **2025 Gross Revenue** values.

    - You set the **2026 Target** at \$18.75B. You distributed the target by
  prior-year weights and applied an H2 skew across **Q3** and **Q4**.

    - You created the **2026 Sales Plan**:
    - You adjusted Summit Lager NA by +8% with a 4% quarterly growth
    trend.

    - You used **Bulk Edit** to apply a +8% Q3 uplift to Beer and Hard
    Seltzer in EU and LA.

    - You added an inline comment to Summit Lager NA. You also enabled the
  **Comments** column and tagged another user in a row-level comment.

    - You built the **Gross Profit** sheet. The sheet uses editable **Sales
  Plan** and **COGS** copies of **2026 Sales Plan** and **2025 COGS**, with **Gross Profit** as a formula measure.

    - You ran the Optimizer with a \$12.5B gross profit target:
    - The Sales Plan increased from \$18,363M to about \$19,203M.
    - COGS decreased from \$7,543M to about \$6,703M.
    - You compared the original and optimized values side by side.
    - (Optional) You configured a two-level approval workflow. You added the
  **Regional Manager** and **Finance Director** levels. You then routed the APAC Beer submission to the finance director for approval.


## Summary

In this lab, you imported the Acme Beverages semantic model and created a SQL database in Microsoft Fabric. You then built a Plan app and configured the semantic model and SQL connections. You created the Plan Intro sheet with Region Name, Category, and Brand rows, date hierarchy columns, and 2025 Gross Revenue values. You then explored the Plan interface. You set a top-down 2026 Target of \$18.75B and distributed it by 2025 revenue weights. You then locked Q1 and Q2 to apply an H2 skew to Q3 and Q4. You built a bottom-up 2026 Sales Plan with an 8% Summit Lager uplift in North America and a 4% quarterly growth trend. You then used Bulk Edit to add an 8% Q3 uplift for Beer and Hard Seltzer in the EU and Latin America. You added inline comments, a Comments column with an @mention, and a Status column to support collaboration. Finally, you built a Gross Profit sheet and ran the Optimizer to reach a \$12.5B gross profit target. The Optimizer increased the Sales Plan from \$18,363M to about \$19,203M and reduced COGS from \$7,543M to about \$6,703M. Optionally, you configured a two-level approval workflow.
