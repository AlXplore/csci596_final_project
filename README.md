# Implementing Recommendation Systems
the GitHub repository for the CSCI596 final project

## Group Menbers
   | Zhufeng Qiu | 6676753248 |
   | Yizhou Wu   |            |
   | Haoran Liu  |            |
   | Zehao Zhao  |            |



## High-level of Recommendation System

### Content-based:
1.	Use characteristics of an item
2.	Recommend items that have similar content to items user liked in the past or items that match pre-defined attributes of the user

### Collaborative filtering:
1.	Build a model from
    
    a user's past behavior (e.g., businesses previously reviewed), and similar decisions made by other users
    
2.	Use the model to predict items that the user may like

    Collaborative: suggestions made to a user utilizing information across the entire user base



## Work flow of our final project (Implementation)
![GitHub](https://raw.githubusercontent.com/ZhufengQiu/csci596_final_project/main/img/work_flow.jpg)
### Content-based: 
1.	Concatenate all the review texts for the business as the document and parse the document, such as removing the punctuations, numbers, stop-words, and extremely rare words.
2.	Measuring word importance using TF-IDF, and represent business (create business profiles) by using a set of most important words (like top 200 words)
3.	Create corresponding user profiles by aggregating the profiles of the businesses that each user reviewed.
4.	Use machine learning techniques to estimate degree to which user would review a business.

### Collaborative Filtering (Harnessing quality judgments of other users):
This time we will implement memory-based approaches
#### (1)	User-based CF
1.	During the training process
    
    1. identify user pairs who are similar using their co-rated businesses without considering their rating scores.
    
    2. compute the Pearson correlation for the user pair candidates 
    
* If the number of potential user pairs is too large to compute in memory, we will combine the Min-Hash and LSH algorithms in the user-based CF recommendation system.

2.	During the predicting process
we will use the model to predict the rating for a given pair of user and business.

#### (2)	Item-based CF
1.	During the training process
    
    we will build a model by computing the Pearson correlation for the business pairs that have at least 3 co-rated users, and our model will only contain the valid pairs that have positive Pearson similarity.
    
2.	During the predicting process
    
    we will use the model and at most three business neighbors to predict the rating for a given pair of user and business.



## The Comparison between Content-based and Memory-based Method

<img width="824" height="363.2" src="https://raw.githubusercontent.com/ZhufengQiu/csci596_final_project/main/img/comparison_between_cb_and_cf.png"/>



# Getting Started
Since review dataset is too large to be analyzed by a single computing node, so the traditional recommendation based on single CPU or one computer node is hard to meet our requirement. Therefore, we will use Apache Spark to implement the distributed computation which can accelerate the calculation speed and avoid memory corruption. In this project, we will use the Spark API through Scala, and the version of Scala is 2.3.2.

# Dataset
we can access Yelp user and business dataset from [Yelp Dataset](https://www.yelp.com/dataset), and in this project, we use the following five files.

1.	train_review.json – train dataset: the user and business pair for training
2.	test_review.json – predict dataset: the target user and business pairs for prediction
3.	stopwords – useless or meaningless words for model
4.	user_avg.json – the average ratings for the users in the train dataset 
5.	business_avg.json – the average ratings for the businesses in the train dataset

the format of train and predict dataset is as follow.

<img width="316" height="380" src="https://raw.githubusercontent.com/ZhufengQiu/csci596_final_project/main/img/data.png"/>

# Scala Script Description
1.	build.sbt – add spark-related dependencies to enable us run tests and package our projects as JAR files.
2.	Collaborative Filtering Recommendation System
 - CollaborativeFilteringTrain.scala – build a user-based or item-based model for recommendation system.
 - CollaborativeFilteringPredict.scala – predict the similarity for the target user and business based on the previous user-based or item-based model
3. Content Based Recommendation System
 - ContentBasedTrain.scala - build a content-based model for recommendation system.
 - ContentBasedPredict.scala - predict the similarity for the target user and business based on the previous user-based or item-based model

# Running project locally and Result
1. Collaborative Filtering Recommendation System
    1. Training process
        - Implementation command
        
             `$ spark-submit --class CollaborativeFilteringTrain csci596-final.jar <train_dataset> <model _filename> <cf_type>`
                
                1. train_dataset:input file which is the train dataset
                2. model _filename: output file which contains user-based or item-based model
                3. cf_type: “user-based” or item-based
        - Result
        
    2. Prdicting process
        - Implementation command
        
             `$ spark-submit --class CollaborativeFilteringPredict csci596-final.jar <train_dataset> <test_ dataset > <model _filename> <output_filename> <cf_type>`
                
                1. train_dataset:input file which is the train dataset
                2. test_ dataset: input file which is the target predict dataset
                3. model _filename: user-based or item-based model file
                4. output_filename: the similarity for the target user and business pairs
                5. cf_type: “user-based” or item-based
        - Result
        
3. Content Based Recommendation System
    1. Training process
        - Implementation command
        
             `$ spark-submit --class task2train hw3.jar < train_dataset > <model_filename> <stopwords>`
                
                1. train_dataset:input file which is the train dataset
                2. model _filename: output file which contains content-based model
                3. stopwords: input file which contains useless or meaningless words
        - Result
    2. Prdicting process
        - Implementation command
                
             `$ spark-submit --class task2predict hw3.jar <test_dataset> <model_filename> <output_file>`
                
                1. test_ dataset: input file which is the target predict dataset
                2. model _filename: output file which contains content-based model
                3. output_file: the similarity for the target user and business pairs
        - Result
    

