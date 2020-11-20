# csci596_final_project
the GitHub repository for the CSCI596 final project

## High-level of Recommendation System

### Content-based:
1.	Use characteristics of an item
2.	Recommend items that have similar content to items user liked in the past or items that match pre-defined attributes of the user

### Collaborative filtering:
1.	Build a model from
>a user's past behavior (e.g., businesses previously reviewed), and similar decisions made by other users
2.	Use the model to predict items that the user may like

Collaborative: suggestions made to a user utilizing information across the entire user base


## Work flow of our final project (Implementation)
### Content-based: 
1.	Concatenate all the review texts for the business as the document and parse the document, such as removing the punctuations, numbers, stop-words, and extremely rare words.
2.	Measuring word importance using TF-IDF, and represent business (create business profiles) by using a set of most important words (like top 200 words)
3.	Create corresponding user profiles by aggregating the profiles of the businesses that each user reviewed.
4.	Use machine learning techniques to estimate degree to which user would review a business.

### Collaborative Filtering (Harnessing quality judgments of other users):
This time we will implement memory-based approaches
### (1)	User-based CF
1.	During the training process
>1.	identify user pairs who are similar using their co-rated businesses without considering their rating scores.
>2.	compute the Pearson correlation for the user pair candidates 
>* If the number of potential user pairs is too large to compute in memory, we will combine the Min-Hash and LSH algorithms in the user-based CF recommendation system.

2.	During the predicting process
we will use the model to predict the rating for a given pair of user and business.

### (2)	Item-based CF
1.	During the training process
>we will build a model by computing the Pearson correlation for the business pairs that have at least 3 co-rated users, and our model will only contain the valid pairs that have positive Pearson similarity.
2.	During the predicting process
>we will use the model and at most three business neighbors to predict the rating for a given pair of user and business.
