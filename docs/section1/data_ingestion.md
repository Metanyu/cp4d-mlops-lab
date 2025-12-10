# 1. Data Ingestion and Preparation

In this lab, you will assume the role of a Data Scientist, who typically trains and evaluates AI models. The following data sets will be used, download them now:

## Required Datasets
- [customer_personal_info_simplified.csv](assets/customer_personal_info_simplified.csv): This dataset captures personal information of the customers such as gender, marital status, income, age, and similar data.
- [customer_data_transactions.csv](assets/customer_data_transactions.csv):  This dataset captures the transaction data for customers.
- [customer_churn_labels.csv](assets/customer_churn_labels.csv): This dataset captures information about whether a specific customer did churn or not.

## Pre-requisites
### Project Creation
!!! info 
    In IBM Cloud Pak for Data, a Project is how you organize your resources to achieve a particular goal. A project allows for high-level isolation, enabling users to package their project assets independently for different use cases or departments. Your project resources can include data, collaborators, scripts, and analytic assets like notebooks and models.


1. Log in CP4D with your assigned user. Contact your instructor if you haven't been assigned one.
2. Go to the Projects section by clicking the Navigation menu and selecting **Projects > All projects.**
   ![Projects](assets/images/ingest/Projects.png)
3. Click **New Project**.
   ![CreateProjects](assets/images/ingest/CreateProjects.png)
4. Insert the following Project Name: **<YourUser> Customer Churn Prediction** where is your username. For example: "user0 Customer Churn Prediction". Then Click **Create**.  
   ![CreateProject](assets/images/ingest/CreateProject.png)
5. Now that the project is created, the Data Scientist will shop for relevant data assets that can help them with training this AI model for customer churn prediction. Click the **Assets** > **Import assets** > **Local file** > **Data asset**.  

    !!! info
        In a real case, the Data Scientist would search for relevant assets in CP4D Catalogs, including assets from external connected sources. However, for simplicity sake, we'll use the 3 datasets referenced earlier.

    ![ImportAssets](assets/images/ingest/ImportAssets.png)
    ![ImportDataAssets](assets/images/ingest/ImportDataAssets.png)

6. Upload the three datasets provided earlier:
   - `customer_personal_info_simplified.csv`
   - `customer_data_transactions.csv`
   - `customer_churn_labels.csv`  
7. Verify the 3 CSV files have been uploaded to your project by clicking the **Assets** tab. You should see all 3 files under the Data assets section:  
   ![CsvUploadProject](assets/images/ingest/CsvUploadProject.png)
8. Next step is to shape the data to get it ready to be used for training ML models.  
   
    !!! info 
        Cloud Pak for Data supports multiple approaches for data shaping and transformation. In this lab, you will use [Data Refinery](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.2.x?topic=data-refining) to cleanse and shape the data with a graphical flow editor and create a joined data set of the Customer Data asset and the labeled churn data set.


## Exercise 1: Data Preparation with Data Refinery
Before creating your ML model, you will prepare data using [Data Refinery](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.2.x?topic=data-refining), a visual UI-based tool that enables users to interactively discover, cleanse and transform data with over 100 built-in operations.

1. Create **New Asset → Data Refinery**.  
    
    !!! tip
        Alternatively, you could access Data Refinery by clicking on the open and close list of options menu (3 vertical dots) next to a data asset in your project and then selecting **Prepare data**.

    ![NewAsset](assets/images/ingest/NewAsset.png)
    ![DataRefineryAsset](assets/images/ingest/DataRefineryAsset.png)

1. Select **Select from project → Data asset → customer_data_transactions.csv** then click **Select**. Enter name for your data refinery asset: `customer_data_transactions.csv_flow`, then select **Create**.
   ![DataRefineryDataAssetSelection](assets/images/ingest/DataRefineryDataAssetSelection.png)
   ![DataRefineryDataAssetCreation](assets/images/ingest/DataRefineryDataAssetCreation.png)
2. This loads the dataset in Data Refinery. Note the tabs Data, Profile, and Visualizations.

- The **Data** tab displays the data and enables you to apply a number of common operations to cleanse and shape the data in a graphical editor. It also supports deploying R library operations, functions, and logical operators via the command line.

- The **Profile** tab shows useful summary statistics including a histogram of each of the data fields. This is useful to understand the statistical distribution of the data as well as potential skew that may exist.

- The **Visualizations** tab provides over 20 customizable charts to provide perspective and insights into the data.  
    ![RefineDataView](assets/images/ingest/RefineDataView.png)

4. Convert `ID` column from Integer → String. This is needed because in the next step when you apply a join of this data and the other data sets, need the column types to match. To do so, click on the **3-dot menu** next to the ID column, select **Convert column type** and then select **String** type and click **Apply**.  
   ![ConvertColumnTypeToString](assets/images/ingest/ConvertColumnTypeToString.png) 
   ![ConvertColumnTypeToString2](assets/images/ingest/ConvertColumnTypeToString2.png) 

5. Next, you will add a step to join this data set with the `customer_personal_info_simplified.csv` dataset to capture additional features that may impact the likelihood of a customer to churn. Click the **New Step** button which will open the operations column, scroll down to find the Join operation and click **Join**. You can also type Join in the Search operations field and it will filter the list of operations to find Join. 
    ![AddANewStep](assets/images/ingest/AddANewStep.png)
    ![AddJoin](assets/images/ingest/AddJoin.png)
6. On the Join operations window, keep the type of join as "*Left join*" then click **Add data set**.  
   ![AddLeftJoin](assets/images/ingest/AddLeftJoin.png)
7. On the Data set page, click **Data asset**, select the `customer_personal_info_simplified.csv` dataset and click **Apply**.  
    !!! tip
        In case you’re not familiar with the Left Join operation, Data Refinery provides an explanation of what that operation does; specifically, a Left Join returns all rows in the original data set and returns only the matching rows in the joining dataset. 

    ![DataSetJoin](assets/images/ingest/DataSetJoin.png)
Back on the Join operation window, click **Select column** to specify `ID` as the field to use for joining the two data sets. Then click **Next**.
    ![DataSetJoinKeys](assets/images/ingest/DataSetJoinKeys.png)

1. On the next window, it shows all the fields that will result from the join operation. At this point, you can remove fields you do not wish to include in the final data set. For this lab, keep all the fields selected and click **Apply**.
   ![DataSetJoinAttributes](assets/images/ingest/DataSetJoinAttributes.png)
2.  Repeat the process (previous 3 steps) to apply a join operation on the resulting data set and the `customer_churn_labels.csv` dataset. The data set to join is `customer_churn_labels.csv`, and the join field is `ID`.
    ![DataSetJoin2](assets/images/ingest/DataSetJoin2.png)
1.  Note that the Data Refinery flow has been augmented with all the executed operations. As you perform more operations to shape the data, they get added to the Data Refinery flow. For this lab, we will just perform the Join operations but typically, you’d perform several other operations to transform the data and make it ready for analytics insights and training machine learning models.  
    ![CompleteSetOfSteps](assets/images/ingest/CompleteSetOfSteps.png)

    !!! info
        In a real-case scenario, data typically requires several more operations to cleanse by removing nulls, filtering rows with missing data, aggregating data across fields, and/or applying a number of different operations. In this lab, the dataset we’re using is already in good shape and the only operations you will apply is to join the customer data (which was already a join of customer personal information and transaction data) and labeled churn data set.  
  
Take a minute to browse the set of supported operations.  

12.  Change the output file name. To do so, click the **Settings** button.
    ![Settings](assets/images/ingest/Settings.png)
13. Go to the tab **Target data set**. Then click **Edit properties**.
    ![TargetDataSet](assets/images/ingest/TargetDataSet.png)
14. In the **Data asset name** field, insert **CUSTOMER_DATA_ready**. Then click **Save** and **Apply**.
    ![DataAssetName](assets/images/ingest/DataAssetName.png)
    ![ApplyTargetDataSet](assets/images/ingest/ApplyTargetDataSet.png)
15. Once you've applied all the operations to transform the data and configured the output, next step is to save the flow and create a job to apply this data refinery flow against the complete data set. To do so, click on **Save and create a job**.
    ![SaveAndCreateAJob](assets/images/ingest/SaveAndCreateAJob.png)

16. Provide a Name for the job **<YourUser> drjob**, and click **Next**.
    ![CreateAJobConfig](assets/images/ingest/CreateAJobConfig.png)

    On the **Configure** tab, review the **Environment** and keep the default selection *Default Data Refinery XS*, then click **Next**. For jobs that require more resources, you can select a larger Environment to run the job.
    ![CreateAJobConfig2](assets/images/ingest/CreateAJobConfig2.png)

    On the **Schedule** tab, keep the Schedule slider set to off, and click **Next**. In this lab, we don't need to run the data refinery job at a given schedule but we'll manually run it as needed and that is why we kept the default selection as off.
    ![CreateAJobConfig3](assets/images/ingest/CreateAJobConfig3.png)

    On the **Notify** tab, keep the Notification off as default. Click **Next**.
    ![CreateAJobConfig4](assets/images/ingest/CreateAJobConfig4.png)

    On the **Review and create** tab, review the job details and click **Create and run**.
    ![RunJobAndSaveFlow](assets/images/ingest/RunJobAndSaveFlow.png)

17. After you click **Create and Run**, navigate to the jobs view to monitor progress by clicking on the Navigation menu and selecting **Jobs**.

    ![GoToJobs](assets/images/ingest/GoToJobs.png)

18. On the **Jobs** page, you can filter the view by selecting whether you want to look at **Active runs, Jobs with active runs, Jobs with finished runs**, or **Finished runs**. Feel free to filter the different views to see the results. Initially, the job will appear in the view **Jobs with active runs** and when it completes, the job will appear in the view **Jobs with finished runs**. Feel free to click on the job name and review the details and status of the run(s).
    ![ViewJobs](assets/images/ingest/ViewJobs.png)

19. Navigate back to the project and click the **Assets** tab. Note the Data Refinery flow, `customer_data_transactions.csv_flow` which is now a project asset and the newly created data asset, `CUSTOMER_DATA_ready.csv`, which was created by running the Data Refinery flow that joined the customer data transactions with the customer personal information and churn labels data sets. Click the `CUSTOMER_DATA_ready.csv` (red arrow) asset to review the data.
    ![ViewJobResult](assets/images/ingest/ViewJobResult.png)
---

## Exercise 2: Exploratory Data Analysis (EDA) with Data Refinery
1. From the project assets, next to previously created dataset `CUSTOMER_DATA_ready.csv`, open list of options menu (3 vertical dots) and then selecting **Prepare data**. This will open the dataset in Data Refinery.
   ![PrepareData](assets/images/ingest/PrepareData.png)
2. Click on the **Visualizations** tab.
In this section, you will create different visualizations to explore the data and understand the relationships between the different features and the target variable, which is customer churn. Take note of the different chart types supported in Data Refinery.
    ![DataRefineryVisualization](assets/images/ingest/DataRefineryVisualization.png)
1. Click on downward arrow next to list of chart types to see all the supported chart types. Select **Box plot**.
    ![VizSelection](assets/images/ingest/VizSelection.png)
2. In the config panel that appears on the left, select Columns as `USAGE` and Split by as `CHURN`. A box plot will be created showing the distribution of USAGE for churned vs non-churned customers.
    ![VizConfig](assets/images/ingest/VizConfig.png)
3. Scroll down the panel to see additional configuration options such as customizing colors, adding titles, and adjusting axis labels. Toggle **Tranpose** ON, name the title as 'Distribution of Usage for Churn vs. Non-churned customers' and Subtitle as 'In Q4 2025'. Take note of the changes to visualization.
   ![VizConfig2](assets/images/ingest/VizConfig2.png)
4. It is encouraged to create additional visualizations to further explore the data and gain insights.


## FAQ 
1. Relate to Data size Data Refinery can handle: 
Any. Data Refinery operates on a sample subset of rows in the data set. The sample size is 1 MB or 10,000 rows, whichever comes first. However, when you run a job for the Data Refinery flow, the entire data set is processed. 