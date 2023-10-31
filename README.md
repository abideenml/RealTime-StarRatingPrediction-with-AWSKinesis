## End to End Real time :clock1: Machine Learning Pipeline to Predict Star Rating of Product Reviews :postbox:

This project is an End-to-End ML pipeline for natural language processing with Amazon **SageMaker**. The main objective of this project is to build an infrastructure for continuous analytics and automating the pipeline. This project involves training and tuning a text classifier to predict the star rating for product reviews using the SOTA **BERT** model for language representation. To build BERT-based NLP text classifier, I used a product reviews dataset where each record contains some review text and a star rating (1-5).  Advanced model training and deployment techniques such as hyper-parameter tuning, **A/B testing**, and **Bandit testing** are also performed. Lastly, a real-time, **streaming analytics** and data science pipeline to perform window-based aggregations and **anomaly detection** is set up. 
 <br/>

<p align="center">
<img src="data/readme_pics/realtime-streaming.png" width="700"/>
</p>



## Table of Contents
  * [What are Telephone-based Social Engineering attacks?](#what-are-telephone-based-social-engineering-attacks)
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

## What-are-Telephone-based-Social-Engineering-attacks

Telephone-based social engineering attacks, also known as phone scams, are a form of cyber attack where malicious actors use the phone as a medium to manipulate individuals or organizations into revealing sensitive information, performing actions, or providing financial gains. These attacks rely on the art of persuasion, psychological manipulation, and impersonation to deceive and exploit victims. 

Each scam type is identified by a set of speech acts that are collectively referred to as a **Scam Signature**. We can define a scam signature as a set of utterances that perform speech acts that are collectively unique to a class of social engineering attacks. These utterances are the key points, fulfilling the goal of the scammer
for that attack. A scam signature uniquely identifies a class of social engineering attacks in the same way that a malware signature uniquely identifies a class of malware. I will use a social engineering detection approach called the **Anti-Social Engineering Tool (ASsET)**, which detects attacks based on the semantic content of the conversation.



## Data Ingestion and Analysis with AWS S3, Redshift, and Athena


<p align="center">
<img src="data/readme_pics/data-ingestion.png" width="700"/>
</p>


## Exploring and visualizing the data with Athena and Matplotlib


1. Which Product Categories are Highest Rated by Average Rating?
2. Which Product Categories Have the Most Reviews?
3. When did each product category become available in the Amazon catalog based on the date of the first review?
4. What is the breakdown of ratings (1-5) per product category?  
5. How Many Reviews per Star Rating? (5, 4, 3, 2, 1) 
6. How Did Star Ratings Change Over Time?
7. Which Star Ratings (1-5) are Most Helpful?
8. Which Products have Most Helpful Reviews?  How Long are the Most Helpful Reviews?
9. What is the Ratio of Positive (5, 4) to Negative (3, 2 ,1) Reviews?
10. Which Customers are Abusing the Review System by Repeatedly Reviewing the Same Product?  What Was Their Average Star Rating for Each Product?

## Building an Automated Data Pipeline with EventBridge and Step functions

<p align="center">
<img src="data/readme_pics/automated-pipeline.png" width="700"/>
</p>


## Testing different models in live production with AB testing

<p align="center">
<img src="data/readme_pics/AB-Testing.png" width="700"/>
</p>

## Dynamically shifting the traffic to better performing BERT model with Multi-Armed Bandits

<p align="center">
<img src="data/readme_pics/multi-armed-bandit.png" width="700"/>
</p>

## Continuous Analytics and ML over Streaming data with AWS Kinesis

<p align="center">
<img src="data/readme_pics/streaming-architecture.png" width="700"/>
</p>

<p align="center">
<img src="data/readme_pics/realtime-streaming.png" width="700"/>
</p>


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