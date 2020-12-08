# Implementing Recommendation Systems
the GitHub repository for the CSCI596 final project

## Group Members
   | Name        | USC-ID     |
   | :---------- | :--------- |
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



## Workflow of our final project (Implementation)
![GitHub](https://raw.githubusercontent.com/ZhufengQiu/csci596_final_project/main/img/work_flow.jpg)
### Content-based: 
1.	Concatenate all the review texts for the business as the document and parse the document, such as removing the punctuations, numbers, stop-words, and extremely rare words.
2.	Measuring word importance using TF-IDF, and represent a business (create business profiles) by using a set of most important words (like top 200 words)
3.	Create corresponding user profiles by aggregating the profiles of the businesses that each user reviewed.
4.	Use machine learning techniques to estimate the rating to which the user would review a business.

### Collaborative Filtering (Harnessing quality judgments of other users):
This time we will implement memory-based approaches.
#### (1)	User-based CF
1.	During the training process
    
    1. Identify user pairs who are similar using their co-rated businesses without considering their rating scores.
    
    2. Compute the Pearson correlation for the user pair candidates 
    
* If the number of potential user pairs is too large to compute in memory, we will combine the Min-Hash and LSH algorithms in the user-based CF recommendation system.

2.	During the predicting process
    
    We will use the model to predict the rating for a given pair of user and business.

#### (2)	Item-based CF
1.	During the training process
    
    We will build a model by computing the Pearson correlation for the business pairs that have at least three co-rated users, and our model will only contain the valid pairs that have positive Pearson similarity.
    
2.	During the predicting process
    
    We will use the model and at most three business neighbors to predict the rating for a given pair of user and business.



## The Comparison between Content-based and Memory-based Method

<img width="824" height="363.2" src="https://raw.githubusercontent.com/ZhufengQiu/csci596_final_project/main/img/comparison_between_cb_and_cf.png"/>



# Getting Started
Since the review dataset is too large to be analyzed by a single computing node, so the traditional recommendation based on a single CPU or one computer node is hard to meet our requirement. Therefore, we will use Apache Spark to implement the distributed computation, which can accelerate the calculation speed and avoid memory corruption. In this project, we will use the Spark API through Scala, and the version of Scala is 2.3.2.

# Dataset
We can access Yelp user and business dataset from [Yelp Dataset](https://www.yelp.com/dataset) or in the [Google Drive](https://drive.google.com/drive/folders/1sbNFFPrTnCC9-zcfxwMD2U5mJxf-0d9A?usp=sharing), and in this project, we use the following five files.

1.	train_review.json – train dataset: the user and business pair for training
2.	test_review.json – predict dataset: the target user and business pairs for prediction
3.	stopwords – useless or meaningless words for model
4.	user_avg.json – the average ratings for the users in the train dataset 
5.	business_avg.json – the average ratings for the businesses in the train dataset

The format of train and predict dataset is as follows.

<div align=center><img width="316" height="380" src="https://raw.githubusercontent.com/ZhufengQiu/csci596_final_project/main/img/data.png"/></div>

# Scala Script Description
1.	build.sbt – add spark-related dependencies to enable us run tests and package our projects as JAR files.
2.	Collaborative Filtering Recommendation System
    - CollaborativeFilteringTrain.scala – build a user-based or item-based model for recommendation system.
        - input: train dataset (user and business pairs with review text)
        - output: user profiles and business profiles, which are defined by using most frequent word
        
    - CollaborativeFilteringPredict.scala – predict the similarity for the target user and business based on the previous user-based or item-based model.
        - input: the model from the training process
        - output: the potential rating to which the user would review a business.
        
3. Content-Based Recommendation System
    - ContentBasedTrain.scala - build a content-based model for recommendation system.
        - input: train dataset (user and business pairs with review text)
        - output: the similarity between different users or between different business
        
    - ContentBasedPredict.scala - predict the similarity for the target user and business based on the previous user-based or item-based model.
        - input: the similarity between different users or between different business
        - output: the potential rating to which the user would review a business.

# Running project locally and Result
Before implementation of the Scala script, we need to package our project.
For IntelliJ IDEA, go to File--->project structure --> Artifacts, then check the content of this project before building the JAR package.
This time, we will name the JAR package as `csci596-final.jar`

1. Collaborative Filtering Recommendation System
    1. Training process
        - Implementation command
        
             `$ spark-submit --class CollaborativeFilteringTrain csci596-final.jar <train_dataset> <model _filename> <cf_type>`
                
                argument list
                    train_dataset: input file which is the train dataset
                    model _filename: output file which contains user-based or item-based model
                    cf_type： “user-based” or “item-based”              
        
    2. Predicting process
        - Implementation command
        
             `$ spark-submit --class CollaborativeFilteringPredict csci596-final.jar <train_dataset> <test_ dataset > <model _filename> <output_filename> <cf_type>`
                
                argument list
                    train_dataset: input file which is the train dataset
                    test_ dataset: input file which is the target predict dataset
                    model _filename: user-based or item-based model file
                    output_filename: the similarity for the target user and business pairs
                    cf_type: “user-based” or “item-based”
        
        - The format of content-based prediction
                
              {"user_id":"mEzc6LeTNiQgIVsq3poMbg","business_id":"9UVkAmyodpaSBm49DBKFNw","sim":0.307655132236926}
              {"user_id":"QtMgqKY_GF3XkOpXonaExA","business_id":"IJUCRd5v-XLkcGrKjb8IfA","sim":0.23060301706458639}
              {"user_id":"nwvnNIixvyYTg4JS8g3Xgg","business_id":"WQyGqfFKd-baBTVfZWzeTw","sim":0.12567127448533824}
              {"user_id":"aZtJzH3fRIRzrGnQRIVaRg","business_id":"bKbYRUZKDYonSPOjzchJJg","sim":0.3219211724504379}
              {"user_id":"Vp5DzIiP_MmaMZMZVFFltA","business_id":"ujHiaprwCQ5ewziu0Vi9rw","sim":0.6395112949403444}
              {"user_id":"srm0YUaJubOLxs4ByEZpwg","business_id":"iCQpiavjjPzJ5_3gPD5Ebg","sim":0.3051479758236465}
              {"user_id":"XCNi6raOHuxmI66Cg2Er2Q","business_id":"YJ8ljUhLsz6CtT_2ORNFmg","sim":0.22512290371929625}
        
3. Content-Based Recommendation System
    1. Training process
        - Implementation command
        
             `$ spark-submit --class task2train hw3.jar < train_dataset > <model_filename> <stopwords>`
                
                argument list
                    train_dataset: input file which is the train dataset
                    model _filename: output file which contains content-based model
                    stopwords: input file which contains useless or meaningless words
       
       - The format of item-based model
                  
              {"b1":"RodA_gxhr2Mj-pyQAL2RAQ","b2":"VMXl3Dm1Nwvgwo1PVYTXbg","sim":0.49999999999999994}
              {"b1":"GmwpYbZBpKn6K5SuQffT6A","b2":"UMqfeCItzQ2glr4d9apGlA","sim":0.5773502691896258}
              {"b1":"HmsCerK_rub0Ulo0aC0f9A","b2":"eLFfWcdb7VkqNyTONksHiQ","sim":0.16666666666666663}
              {"b1":"YXohNvMTCmGhFMSQsDZq1g","b2":"UGy0QULAPPRcOUJQSiRmGQ","sim":1.0}
              {"b1":"tQifTiY-vutj8orxcMJKfQ","b2":"qkyCuFJF2Uboh6n2Lmuwlg","sim":0.3461538461538461}
              {"b1":"g8OnV26ywJlZpezdBnOWUQ","b2":"bgXb5YYzQvHXmM8owZB4fQ","sim":0.4178554470186725}
              {"b1":"n8Zqqhff-2cxzWt_nwhU2Q","b2":"E83nSU_y9zedOzQnkTjV1g","sim":0.2738612787525831}
              {"b1":"TL5Dtfnf-5hYG13lBF9N2A","b2":"8K3CRM4COa0SSBEvli0fJQ","sim":0.6882472016116852}
       
       - The format of user-based model
              
              {"u1":"kkTOV5bnE9hHb5V_zd9b9g","u2":"kVyOe_qIsGAlk0wo9n9_9g","sim":0.5}
              {"u1":"dAfdS9l7DOMze3orptDxiQ","u2":"AyjqBovADgbskmLrIBOMlQ","sim":0.11322770341445963}
              {"u1":"WJjUk1-gib1AD4u-q2VNYA","u2":"n78PhLGZAiBiQIErPNMn2g","sim":0.6933752452815363}
              {"u1":"pnTiEaqM4slogpY97n9Kvg","u2":"Pf7FI0OukC_CEcCz0ZxoUw","sim":0.42640143271122083}
              {"u1":"BI4jBJVto2tEQ0NiaR0rNQ","u2":"Y3PQ5P_R7jcgiIS_1dIv-w","sim":0.43386091563731244}
              {"u1":"BvVAypE18TeuMdF1aDw51g","u2":"WfbJKGJWj2yQ50XRku-OzQ","sim":0.7559289460184545}
              {"u1":"R4Jif1Q7vseoA2TqfzeQPQ","u2":"HRyFtrmf19GTKEM_kOa2pg","sim":0.9999999999999999}
        
    2. Prdicting process
        - Implementation command
                
             `$ spark-submit --class task2predict hw3.jar <test_dataset> <model_filename> <output_file>`
                
                argument list
                    test_ dataset: input file which is the target predict dataset
                    model _filename: output file which contains content-based model
                    output_file: the similarity for the target user and business pairs
        
        - The format of item-based prediction
        
              {"user_id":"mEzc6LeTNiQgIVsq3poMbg","business_id":"9UVkAmyodpaSBm49DBKFNw","stars":3.188381266947627}
              {"user_id":"QtMgqKY_GF3XkOpXonaExA","business_id":"IJUCRd5v-XLkcGrKjb8IfA","stars":4.071286541577826}
              {"user_id":"nwvnNIixvyYTg4JS8g3Xgg","business_id":"WQyGqfFKd-baBTVfZWzeTw","stars":3.3484779210974254}
              {"user_id":"aZtJzH3fRIRzrGnQRIVaRg","business_id":"bKbYRUZKDYonSPOjzchJJg","stars":4.654191548467289}
              {"user_id":"Vp5DzIiP_MmaMZMZVFFltA","business_id":"ujHiaprwCQ5ewziu0Vi9rw","stars":4.207684051798747}
              {"user_id":"srm0YUaJubOLxs4ByEZpwg","business_id":"iCQpiavjjPzJ5_3gPD5Ebg","stars":3.816061612269136}
              {"user_id":"XCNi6raOHuxmI66Cg2Er2Q","business_id":"YJ8ljUhLsz6CtT_2ORNFmg","stars":4.013062460330373}
        
        - The format of item-based prediction

              {"user_id":"QuZbJquRtbY9O9JrB9NpxQ","business_id":"6EWqsE6Ojvt-FA-rl8Sm4Q","stars":3.678432536992683}
              {"user_id":"kwIhn1_cnQeUaLN0CuWWHw","business_id":"TKcq0hOqesiDOwQb-ZwFAw","stars":3.574360696549783}
              {"user_id":"7fyAtRSYYqOKZPr39L2UYg","business_id":"uGupeWqih0yIcCg8anM1PA","stars":4.081740261115626}
              {"user_id":"iSC96O2NjQc3JExGUHQG0Q","business_id":"_gMR_YdsMwwKy63-UwjdnA","stars":3.931358147592911}
              {"user_id":"rkZ_FQ1yWxPOm3JCv_wKwQ","business_id":"5eV8oUGdBXylwB7HeaDFOA","stars":4.080688164649474}
              {"user_id":"Rt9-31f2wdutUiIrO11_Jw","business_id":"IB8zLlGraOg9LU7qQVLPyg","stars":3.963358222967877}
              {"user_id":"vWP8-aQX0rYJszqkWVL7Ew","business_id":"rF183_3e0ybElRKdNSwJqQ","stars":3.087735813372279}
