# 4. Model Monitoring

## Exercise: Enable model monitoring for an online deployment through OpenScale

!!! info
    A key component of a Governed MLOps solution is the ability to monitor AI models for accuracy, fairness, explainability and drift. These capabilities deliver trustworthy AI which business leaders can safely adopt in their business processes and customer engagements.

    With multiple client engagements, we have found that having the confidence to trust AI models is just as important, and sometime even more important, than the performance of the AI models. Watson OpenScale, a component of Watson Studio and Cloud Pak for Data, is IBM’s solution to deliver trustworthy AI and enable monitoring of AI models for fairness, explainability and drift.

In this lab you will learn how to leverage Watson OpenScale to monitor the churn prediction model we previously deployed to Watson Machine Learning.

### Prerequisites
Later in this lab, you're going to provide OpenScale the training data of the model. Watson OpenScale supports reading training data from a CSV file, from Db2, and from Cloud Object Storage (COS). The training data in this workshop is stored locally in a CSV file.

!!! info

    Review [Why does OpenScale need access to my training data](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.2.x?topic=models-faqs#trainingdata) for more details on why the training data is needed. 

### Enabling monitoring for our model
1. Go to menu **Deployments**:

    ![menuDeployments](assets/images/openscale/menuDeployments.png)

2. Open the **churnUASpace** space where you deployed your initial AutoAI-generated Model for testing.

    ![churnUATSpace](assets/images/openscale/churnUATSpace.png)

3. In the **Deployments** tab, open any of the **Churn Deployment** deployment. We're going to monitor this deployed model.

    ![openYourDeployment](assets/images/openscale/openYourDeployment.png)

4. Go the **Evaluations** tab and click **Configure OpenScale evaluation settings**.

    ![openOpenscaleSettings](assets/images/openscale/openOpenscaleSettings.png)

    !!! warning
        If you see the following message, click Associate a server instance:
        ![associateServerInstance](assets/images/openscale/associateServerInstance.png)

    The following menu will appear. You're going to configure the monitors in the following section.

    ![openScaleMenu](assets/images/openscale/openScaleMenu.png)

### Model Configuration
1. Click on Reconfigure model to open the Model Configuration window.

    ![reconfigModel](assets/images/openscale/reconfigModel.png)

2. In the Model Configuration window, select **Use manual setup** and click **Next**.

    ![ManualSetup](assets/images/openscale/ManualSetup.png)

2. For the simplicity of this workshop, we're going to upload the training data from a CSV file. Select **Upload file** and upload [customer_training_data.csv] .

    ![ConfigModelTrainData](assets/images/openscale/ConfigModelTrainData.png)

3. For the feature columns, accept the default which selects all the features. Click **Next**.

    ![ConfigModelTrainData2](assets/images/openscale/ConfigModelTrainData2.png)

4. For the model output, select probability and predictedLabel as shown below. Then click **Next**.
    
    ![ConfigModelTrainData3](assets/images/openscale/ConfigModelTrainData3.png)

The training data has been loaded to OpenScale.

### Configure Quality
Let's configure the Quality controls for our model.

!!! info

    Watson OpenScale can monitor the Quality metric which measures the model’s ability to correctly predict outcomes that match labeled data.

1. Select **Quality** from the Evaluations section. Then click the pencil icon.

    ![image29](assets/images/openscale/image29.png)

2. Specify the Threshold value for **Area under ROC** to be **0.9** and click **Next**. This means that when the Area under ROC is less than 0.9, the quality monitor will flag an alert.

    ![image30](assets/images/openscale/image30.png)

3. Change the Minimum sample size to 100. In production, you should use larger sample sizes to make sure they’re representative of the requests the model receives. Click Save.

    ![image31](assets/images/openscale/image31.png)

### Configure Fairness
Let's configure the Fairness controls for our model.

!!! info
    In the fairness monitor, you specify to Watson OpenScale which features to monitor and what are the favourable outcomes. The Watson OpenScale fairness monitor determines whether outcomes that are produced by your model are fair or not for monitored group. When fairness monitoring is enabled, it generates a set of metrics every hour by default. You can generate these metrics on demand by clicking the **Check fairness now** button or by using the Python client.

    Watson OpenScale automatically identifies whether any known protected attributes are present in a model. When Watson OpenScale detects these attributes, it automatically recommends configuring bias monitors for each attribute present, to ensure that bias against these potentially sensitive attributes is tracked in production.

    Currently, Watson OpenScale detects and recommends monitors for the following protected attributes (sex, ethnicity, marital status, age, zip code).

1. Select **Fairness** in the Evaluations section and click the pencil icon to configure the fairness monitor.

    ![image33](assets/images/openscale/image33.png)

2. Select **Configure manually** then click **Next**.

    ![image33b](assets/images/openscale/image33b.png)

3. Next, select the favorable outcomes, specify **F** (false) as Favorable value (client is not going to churn), and **T** (true) as Unfavorable value. Then click **Next**.

    ![image34](assets/images/openscale/image34.png)

4. Next, select the **Minimum sample size** to be **100**. In production, you may want to select a larger sample size to make sure it is representative.

    ![image35](assets/images/openscale/image35.png)

5. Keep the default selections set to Disparate impact. Then Click **Next**.

    ![image35b](assets/images/openscale/image35b.png)

6. Leave the default values and click **Next**.

    ![image35c](assets/images/openscale/image35c.png)

7. OpenScale automatically detected the features GENDER and AGE as candidates for being monitored for fairness by analyzing the training data. Click **Next**.

    ![image36](assets/images/openscale/image36.png)
8. For the AGE feature, you can specify the reference and monitored groups. Again, Watson OpenScale automatically recommends which group should be the reference and which group(s) should be monitored by analyzing the training data. Accept the default selections by Watson OpenScale. Click **Next**.

    ![image37](assets/images/openscale/image37.png)

9. Set the fairness alert threshold for AGE to 95 which effectively indicates that OpenScale will raise an alert when the model predicts a favorable outcome for the monitored group 95% of the times less than a favorable outcome for the reference group.

    ![image37b](assets/images/openscale/image37b.png)

    !!! info
        Check the [How it works](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.2.x?topic=evaluations-calculating-fairness) section in the documentation to better understand how fairness of the model is computed by OpenScale.

10. Next, for the GENDER feature, specify the reference and monitored groups as **F** Monitored and **M** Reference. Then click **Next**.

    ![image38](assets/images/openscale/image38.png)

11. Specify the fairness alert threshold to be 95 and click **Save**.

    ![image38b](assets/images/openscale/image38b.png)

    !!! optional
        You could also configure the Drift monitors. For details on Drift monitors, check the [Drift documentation page](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.2.x?topic=models-configuring-drift-evaluations).

### Configure Drift
1. Go to the **Drift v2** tab. Then click the pencil icon to configure it.

    ![drift01](assets/images/openscale/drift01.png)


2. In this case, we'll use OpenScale directly to calculate possible Drift. **Select Compute in Watson OpenScale** and click **Next**.

    ![drift02](assets/images/openscale/drift02.png)

3. Leave the default values. Feel free to read the description of these thresholds. Click **Next**.

    ![drift03](assets/images/openscale/drift03.png)

4. In this case, all features will be considered important to detect drift. Select all features and click **Next**.

    ![drift04](assets/images/openscale/drift04.png)

5. For the root cause analysis, select the following features:

   - AGE
   - ESTINCOME
   - GENDER
   - RATEPLAN
   - USAGE
   Then click **Next**.

    ![drift05](assets/images/openscale/drift05.png)

6. Click **Save**.

    ![drift06](assets/images/openscale/drift06.png)

### Configure Explainability
1. Go to the **General settings** option under the Explainability section. Then click the pencil icon to configure it.

    ![image100](assets/images/openscale/image100.png)

    !!! info
        Two different methods are available for explanations: [Shapley Additive Explanations (SHAP)](https://shap.readthedocs.io/en/latest/) or [Local Interpretable Model-agnostic Explanations (LIME)](https://www.openlayer.com/blog/post/understanding-lime-in-5-steps).

        As described in the hint that appears when you click the Information box, SHAP often provides more thorough explanations, but LIME is faster.

2. For this workshop, we'll use the LIME explanation method as it's faster. Click **Next**.

    ![image101](assets/images/openscale/image101.png)

3. All features will be analyzed in this case. Click **Save**.
    
    ![image102](assets/images/openscale/image102.png)

### Evaluating the model
Now that the model monitors have been configured, you can run an evaluation of the model.

1. Click the **X** button to close the Configure monitors window.

    ![image40](assets/images/openscale/image40.png)

2. Click on the **Actions** button then **Evaluate now**.

    ![image40b](assets/images/openscale/image40b.png)

3. On the Import test data page, choose **from CSV file** and click **Browse**.

    ![image41](assets/images/openscale/image41.png)

4. Select the [customer_churn_openscale_evaluation.csv](assets/customer_churn_openscale_evaluation.csv) file that you downloaded at the beginning of the workshop. Then click Upload and evaluate.

    ![image41b](assets/images/openscale/image41b.png)

    !!! info
        OpenScale will upload the data, run scoring against it and compare the model prediction to the labeled result to compute an overall quality score. It will also run the Fairness monitor to detect any fairness violations. Once the evaluation complete, you get a quick view in the dashboard of the Fairness and Quality results.

5. Check the results of the evaluation. in the example below, it shows no alerts Quality, meaning the model meets or exceeds the required thresholds set for those monitors. However, there's an alert for Fairness and two alerts for Drift.

    !!! warning
        Note that your actual results may be different, and you may see different alerts.

    ![image42](assets/images/openscale/image42.png)

### Reviewing Fairness and Explainability
1. Click the arrow next to the Fairness monitor to review the fairness results further.

    ![image43](assets/images/openscale/image43.png)

2. In this case, no bias was detected in the GENDER feature.

    ![image44](assets/images/openscale/image44.png)

3. Click the **Monitored attribute** option and change it to the **AGE** feature.

    ![image45](assets/images/openscale/image45.png)

    !!! info
        In this case, a potential bias was found in the model against people in the first age group. Note that the thresholds we set in our monitors are key to decide when OpenScale is going to trigger alerts.

4. Click the **View payload transactions** button.

    ![image46](assets/images/openscale/image46.png)

5. These are the transactions that were evaluated. Choose one and click **Explain prediction**. In this example, we'll choose one with individual bias detected.

    ![image47](assets/images/openscale/image47.png)

6. This graph explains how much each feature impacted the decision.

    ![image48](assets/images/openscale/image48.png)

    Go to tab Inspect.

    ![image49](assets/images/openscale/image49.png)

7. In this section you can investigate how features should change for the prediction outcome to change. In the example below, this customer is predicted to churn (Predicted Outcom is T = True). Click **Run analysis**.

    ![image50](assets/images/openscale/image50.png)

8. In this case, the STATUS feature should change from 'M' to 'S' for the model to predict no-churn instead of churn.

    ![image51](assets/images/openscale/image51.png)

9. You can also investigate what the prediction would be if a specific feature changes to another value. For example, let's **change the AGE from 46 to 18**. Then, click **Score new values** to check what would be predicted in that case:

    ![image52](assets/images/openscale/image52.png)

10. The prediction would change to no-churn in that case, with a confidence of 60%.

    ![image53](assets/images/openscale/image53.png)

### Reviewing Drift
1. Go back to your deployment by clicking its name in the breadcrumb menu.

    ![goToDeployment](assets/images/openscale/goToDeployment.png)

2. Go to the **Evaluations** tab then click the arrow in the **Drift v2** section to open the drift test details.

    ![enterDrift](assets/images/openscale/enterDrift.png)

3. Review the charts in this page. First, **History** chart.

    ![driftHistory](assets/images/openscale/driftHistory.png)

    !!! info
        This graph shows how the drift for the prediction (output drift), the incoming/evaluation data (feature drift), and the model quality has evolved in time. Only one evaluation was run in this case.

4. Scroll down to the **Output drift** chart. Note that you can see drift charts for each predicted outcome by using the dropdown option:

    ![driftOutput-1](assets/images/openscale/driftOutput-1.png)

    ![driftOutput-2](assets/images/openscale/driftOutput-2.png)

    !!! info
        This graph shows how the prediction of the model has drifted from the training data to the input/evaluation data.

        In this case, we have a drift issue. We can investigate it further using the following graphs.

5. Scroll down to the **Feature drift** chart.

    ![driftFeature](assets/images/openscale/driftFeature.png)

    !!! info
        This graph shows how the input/evaluated data has changed incomparison with the data that was used to train the model. This is useful to get an idea on which changes in data may have affected the model prediction accuracy, reducing analysis and investigation time.

        In this example, the feature LOCAL has significant changes.

6. Scroll down to the following charts.

    ![driftFeatureDetails](assets/images/openscale/driftFeatureDetails.png)

    ![driftFeatureDetails-2](assets/images/openscale/driftFeatureDetails-2.png)
    
    !!! info
        For each feature with drift, the amount of changes for each range of values is shown. Besides, the evolution this feature's drift over time is shown in the second graph.