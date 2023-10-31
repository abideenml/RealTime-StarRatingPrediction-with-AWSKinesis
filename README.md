## End to End Real time :clock1: Machine Learning Pipeline to Predict Star Rating of Product Reviews :postbox:

This project is an End-to-End ML pipeline for natural language processing with Amazon **SageMaker**. The main objective of this project is to build an infrastructure for continuous analytics and automating the pipeline. This project involves training and tuning a text classifier to predict the star rating for product reviews using the SOTA **BERT** model for language representation. To build BERT-based NLP text classifier, I used a product reviews dataset where each record contains some review text and a star rating (1-5).  Advanced model training and deployment techniques such as hyper-parameter tuning, **A/B testing**, and **Bandit testing** are also performed. Lastly, a real-time, **streaming analytics** and data science pipeline to perform window-based aggregations and **anomaly detection** is set up. 
 <br/>

<p align="center">
<img src="data/readme_pics/realtime-streaming.png" width="700"/>
</p>



## Table of Contents
  * [Data Ingestion and Analysis with AWS S3, Redshift, and Athena](#data-ingestion-and-analysis-with-aws-s3,-redshift,-and-athena)
  * [Exploring and visualizing the data with Athena and Matplotlib](#exploring-and-visualizing-the-data-with-athena-and-matplotlib)
  * [Building an Automated Data Pipeline with EventBridge and Step functions](#building-an-automated-data-pipeline-with-eventbridge-and-step-functions)
  * [Testing different models in live production with AB testing](#testing-different-models-in-live-production-with-ab-testing)
  * [Dynamically shifting the traffic to better performing BERT model with Multi-Armed Bandits](#dynamically-shifting-the-traffic-to-better-performing-bert-model-with-multi-armed-bandits)
  * [Continuous Analytics and ML over Streaming data with AWS Kinesis](#continuous-analytics-and-ml-over-streaming-data-with-aws-kinesis)
  * [Setup](#setup)
  * [Todos](#todos)
  * [Acknowledgements](#acknowledgements)
  * [Citation](#citation)
  * [Connect with me](#connect-with-me)



## Data Ingestion and Analysis with AWS S3, Redshift, and Athena

For data ingestion, I have used Amazon S3 as the central data lake where I store all raw data in TSV/Parquet format. This data needs to be accessed by both the data science / machine learning team, as well as the business intelligence / data analyst team.

**Data science or Machine learning** teams need access to all of the raw data, and be able to quickly explore it. They leverage **Amazon Athena** as an interactive query service to analyze data in Amazon S3 using standard SQL, without moving the data. 


**Business intelligence team and Data analysts** mostly want to have a subset of the data in a data warehouse which they can then transform, and query with their standard SQL clients to create reports and visualize trends. I have leveraged **Amazon Redshift**, a fully managed data warehouse service.

Explore data ingestion notebook `Ingest.ipynb` for more details.

<p align="center">
<img src="data/readme_pics/data-ingestion.png" width="700"/>
</p>




## Exploring and visualizing the data with Athena and Matplotlib

This section invloves exploring, visualizing and understanding part of the raw data with Athena and Matplotlib. I have covered these important questions regarding the problem statement and raw data:

1. Which Product Categories are Highest Rated by Average Rating?
2. Which Product Categories Have the Most Reviews?
3. When did each product category become available in the Data catalog based on the date of the first review?
4. What is the breakdown of ratings (1-5) per product category?  
5. How Many Reviews per Star Rating? (5, 4, 3, 2, 1) 
6. How Did Star Ratings Change Over Time?
7. Which Star Ratings (1-5) are Most Helpful?
8. Which Products have Most Helpful Reviews?  How Long are the Most Helpful Reviews?
9. What is the Ratio of Positive (5, 4) to Negative (3, 2 ,1) Reviews?
10. Which Customers are Abusing the Review System by Repeatedly Reviewing the Same Product?  What Was Their Average Star Rating for Each Product?


Explore data ingestion notebook `Analysis.ipynb` for more details.

## Building an Automated Data Pipeline with EventBridge and Step functions

This section involves creating an automated training and deployment pipeline. I have used AWS Step functions for this. The AWS Step Functions Data Science SDK enables us to easily construct and run machine learning workflows that use AWS infrastructure directly in Python, instantiate common training pipelines, create standard machine learning workflows in a Jupyter notebook from templates. Using this SDK we can create steps, chain them together to create a workflow, create that workflow in AWS Step Functions, and execute the workflow in the AWS cloud.

<p align="center">
<img src="data/readme_pics/pipeline_created.png" width="700"/>
</p>



### Tensorboard training visualization
<p align="center">
<img src="data/readme_pics/tensorboard.png" width="700"/>
</p>


To automate the entire training and deployment process, I have used AWS EventBridge. AWS S3 is acting as EventBridge source and Step function is target. The AWS Cloudtrail logs the events. Amazon EventBridge is a serverless event bus that makes it easy to connect applications together using data from our own applications, integrated Software-as-a-Service (SaaS) applications, and AWS services.We can choose an event source (i.e. Amazon S3) and select a target from a number of AWS services including AWS Step Functions, AWS Lambda, Amazon SNS, and Amazon Kinesis Data Firehose. Amazon EventBridge will automatically deliver the events in near real-time.


<p align="center">
<img src="data/readme_pics/automated-pipeline.png" width="700"/>
</p>

Explore training and deployment pipeline notebook `TrainDeploy_Pipeline.ipynb` for more details.

## Testing different models in live production with A/B testing

A/B testing is a statistical approach for comparing two or more versions/features to evaluate not only which one works better but also if the difference is statistically significant. A/B testing can be used for a variety of purposes like: refining the messaging and design of marketing campaigns, increasing conversion rates by improving the user experience, consider user involvement while optimizing assets such as web pages, ads, etc.

This section involves A/B testing two different models which have been trained on different subsets of data. Model A has been trained on one month old data and Model B has been trained on most recent data. We have used traffic splitting to direct subsets of users to different model variants for the purpose of comparing and testing different models in live production. The goal is to see which variants perform better. Often, these tests need to run for a long period of time (weeks) to be statistically significant. The figure shows 2 different models deployed using a random 50-50 traffic split between the 2 variants.

<p align="center">
<img src="data/readme_pics/AB-Testing.png" width="700"/>
</p>

Explore AB testing notebook `AB-Test.ipynb` for more details.

## Dynamically shifting the traffic to better performing BERT model with Multi-Armed Bandits

Unlike traditional A/B tests, the bandit model will learn the best BERT model (action) for a given context over time and begin to shift traffic to the best model.  Depending on the aggressiveness of the bandit model algorithm selected, the bandit model will continuously explore the under-performing models, but start to favor and exploit the over-performing models.  And unlike A/B tests, multi-armed bandits allow you to add a new action (ie. BERT model) dynamically throughout the life of the experiment.  When the bandit model sees the new BERT model, it will start sending traffic and exploring the accuracy of the new BERT model - alongside the existing BERT models in the experiment.

 This implementation continuously updates a Vowpal Wabbit reinforcement learning model using Amazon SageMaker, DynamoDB, Kinesis, and S3.

The client application, a recommender system with a review service in our case, pings the SageMaker hosting endpoint that is serving the bandit model.  The application sends the an `event` with the `context` (ie. user, product, and review text) to the bandit model and receives a recommended action from the bandit model.  In our case, the action is 1 of 2 BERT models that we are testing.  The bandit model stores this event data (given context and recommended action) in S3 using Amazon Kinesis. 

The client application uses the recommended BERT model to classify the review text as star rating 1 through 5 and  compares the predicted star rating to the user-selected star rating.  If the BERT model correctly predicts the star rating of the review text (ie. matches the user-selected star rating), then the bandit model is rewarded with `reward=1`.  If the BERT model incorrectly classifies the star rating of the review text, the bandit model is not rewarded (`reward=0`).

The client application stores the rewards data in S3 using Amazon Kinesis.  Periodically (ie. every 100 rewards), we incrementally train an updated bandit model with the latest the reward and event data.  This updated bandit model is evaluated against the current model using a holdout dataset of rewards and events.  If the bandit model accuracy is above a given threshold relative to the existing model, it is automatically deployed in a blue/green manner with no downtime.  SageMaker RL supports offline evaluation by performing counterfactual analysis (CFA).  By default, we apply [**doubly robust (DR) estimation**](https://arxiv.org/pdf/1103.4601.pdf) method. The bandit model tries to minimize the cost (`1 - reward`), so a smaller evaluation score indicates better bandit model performance.


<p align="center">
<img src="data/readme_pics/multi-armed-bandit.png" width="700"/>
</p>

Explore multi armed bandit notebook `Bandit-Test.ipynb` for more details.

## Continuous Analytics and ML over Streaming data with AWS Kinesis

In this section, I have tried to move from customer reviews training dataset into a real-world scenario. Customer feedback about products appear in all of a company's social media channels, on partner websites, in customer support messages etc. We need to capture this valuable customer sentiment about our products as quickly as possible to spot trends and react fast. I have focused on analyzing a continuous stream of product review messages that is collected from all available online channels. In this project, I have used `Kinesis Data Firehose` to prepare and load the data continuously to a destination of your choice. With `Kinesis Data Analytics`, I have processed and analyzed the data as it arrives. And I have used `Kinesis Data Streams` to deal with ingestion of data streams for custom applications.

<p align="center">
<img src="data/readme_pics/streaming-architecture.png" width="700"/>
</p>

In the first step, I have analyzed the sentiment of the customer, so we can identify which customers  might need high-priority attention. Next, we run continuous streaming analytics over the incoming review messages to capture the average sentiment per product category. We visualize the continuous average sentiment in a `metrics dashboard` for the line of business owners. The line of business owners can now detect sentiment trends quickly,  and take action. We also calculate an anomaly score of the incoming messages to detect anomalies in the data schema or data values. In case of a rising `anomaly score`, we can alert the application developers in charge to investigate the root cause. As a last metric, we also calculate a continuous `approximate count` of the received messages. This number of online messages could be used by the digital marketing team to measure effectiveness of social media campaigns.

<p align="center">
<img src="data/readme_pics/realtime-streaming.png" width="700"/>
</p>


Explore streaming analytics notebook `StreamingAnalytics.ipynb` for more details.


## Setup

So we talked about what telephone based social engineering attacks are, and what they can do for you (among other things). <br/>
Let's get this thing running! Follow the next steps:

1. Create an AWS account and launch Sagemaker studio.
2. Configure IAM to run the notebooks. Attach `AdministratorAccess` policy.
3. Launch a terminal in your Sagemaker Jupyter instance.
4. `git clone https://github.com/abideenml/RealTime-StarRatingPrediction-with-AWSKinesis`
5. Navigate into project directory `cd path_to_repo`
6. Create a new venv environment and run `pip install -r requirements.txt`. 
7. Run the `Ingest.ipynb`, `Analysis.ipynb`, `TrainDeploy_Pipeline.ipynb`, `StreamingAnalytics.ipynb`, `AB-Test`, and `Bandit-Test` files in order for ingestion, exploration, model training, realtime model prediction, AB testing and bandit testing.

That's it! <br/>





## Todos:

Finally there are a couple more todos which I'll hopefully add really soon:
* Test Data quality with Deequ and also add workflow to capture data drift.
* Make AWS QuickSight Dashboard to view KPIs and others metrics.
* Use Kubeflow for managing machine learning workflows.





## Acknowledgements

I found these resources useful (while developing this one):

* [BERT Paper](https://arxiv.org/abs/1810.04805)
* [AWS Sagemaker](https://towardsdatascience.com/aws-sagemaker-db5451e02a79)
* [Multi-Armed Bandit](https://www.youtube.com/watch?v=e3L4VocZnnQ&ab_channel=ritvikmath)
* [AWS Kinesis](https://www.youtube.com/watch?v=_t3k6oX2mfc&t=361s&ab_channel=JohnnyChivers)
* [AWS Serverless](https://faun.pub/aws-lambda-serverless-framework-python-part-1-a-step-by-step-hello-world-4182202aba4a)
* [Working with Contextual Bandits](https://vowpalwabbit.org/docs/vowpal_wabbit/python/latest/tutorials/python_Contextual_bandits_and_Vowpal_Wabbit.html)


## Citation

If you find this code useful, please cite the following:

```
@misc{Zain2023Realtime-ratingprediction-productreviews,
  author = {Zain, Abideen},
  title = {realtime-ratingprediction-productreviews},
  year = {2023},
  publisher = {GitHub},
  journal = {GitHub repository},
  howpublished = {\url{https://github.com/abideenml/RealTime-StarRatingPrediction-with-AWSKinesis}},
}
```

## Connect with me

If you'd love to have some more AI-related content in your life :nerd_face:, consider:

* Connect and reach me on [LinkedIn](https://www.linkedin.com/in/zaiinulabideen/) and [Twitter](https://twitter.com/zaynismm)
* Follow me on 📚 [Medium](https://medium.com/@zaiinn440)
* Subscribe to my 📢 weekly [AI newsletter](https://rethinkai.substack.com/)!

## Licence

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/abideenml/RealTime-StarRatingPrediction-with-AWSKinesis/blob/master/LICENCE)