# FYP

## 1. Movie Recommendation System using Machine Learning **DOI:** [10.1109/ICIRCA54612.2022.9985512](https://webvpn.xmu.edu.cn/https/77726476706e69737468656265737421f4f848d228226f/10.1109/ICIRCA54612.2022.9985512)

This study is to design a movie recommendation system using collaborative filtering algorithm, we can consider two approaches:

1. **User-based Collaborative Filtering:**

In this approach, when recommending movies to a user, we compare the similarity of preferences among users. By finding the most similar users, we can recommend movies that these similar users have liked to the target user.

2. **Item-based Collaborative Filtering:**

In this approach, if a user highly rates a particular movie, we leverage the ratings of all users to identify movies that are most similar to the highly-rated movie. These similar movies are then recommended to the user.

However, collaborative filtering algorithms have certain challenges:

- High computational complexity: Calculating similarities between users or items can be computationally expensive, especially with large datasets.
- Sparse data problem: When the rating data is sparse, i.e., many movies have limited ratings, it becomes challenging to calculate accurate similarities.
- Over-reliance on similar ratings: If a movie is highly rated by a single user, it may lead to inflated similarities. To address this, techniques like SVD can be applied to mitigate the impact of such outliers.

## 2. Similarity Based Collaborative Filtering Model for Movie Recommendation Systems **DOI:** [10.1109/ICICCS51141.2021.9432354](https://webvpn.xmu.edu.cn/https/77726476706e69737468656265737421f4f848d228226f/10.1109/ICICCS51141.2021.9432354)

This study aimed to enhance the performance and accuracy of the recommendation system (RS) and conducted further experiments to evaluate and analyze the performance of various benchmark similarity metrics, such as Pearson correlation, Euclidean distance, cosine similarity, and Jaccard distance.

**Experimental Conclusion:** For datasets like MovieLens, which are diverse and not normalized, the Pearson correlation score outperformed other similarity metrics.

## 3. Machine Learning Based Movie Recommendation System**DOI:** [10.1109/UPCON52273.2021.9667602](https://webvpn.xmu.edu.cn/https/77726476706e69737468656265737421f4f848d228226f/10.1109/UPCON52273.2021.9667602)

This study is also designing a movie recommendation system, the main aim of this system is to recommend movies to the users based on what they search. Content-based filtering and Collaborative filtering are the main approaches for recommending movies.  It introduces to  Collaborative Filtering based Movie Recommendation Systems and how to calculate the simarity between one movie to the other movie. It proposed a model based on this method and implement it. 



## 4. Movie Recommendation System Using Collaborative Filtering**DOI:** [10.1109/ICSESS.2018.8663822](https://webvpn.xmu.edu.cn/https/77726476706e69737468656265737421f4f848d228226f/10.1109/ICSESS.2018.8663822)

This paper is to design a movie recommendation system that considers the past movie ratings given by various users to provide suggestions to the user. They implemented this system using collaborative filtering algorithms and Apache Mahout framework. The second goal is to compare the performance and efficiency of user-based recommender system and item-based recommender system.

This paper is organized as follows: First, a brief overview of a few relevant, recent research done in the space of recommender system will be discussed. Second,  the understanding on the technique of collaborative filtering. Third, the data preparation and data analysis approach using Mahout will be discussed. Finally, a qualitative evaluation on the techniques used will be presented.



## 5. Machine Learning Based Personalized Movie Research and Implementation of Recommendation System **DOI:** [10.1109/CoST57098.2022.00025](https://doi.org/10.1109/CoST57098.2022.00025)

This paper is also design a movie recommendation system.  It only focus on User-Based Collaborative Filtering Algorithm

The rating data is preprocessed and visualized in consideration of the user's real behavior. Then implement the algorithm mentioned above, and use the test indicators to measure the performance of the recommender system and optimize the system parameters. Finally, using software engineering and Java front-end knowledge based on Spring+SpringMVC+Mybaits (SSM) to develop a demo system.



## 6. HCB Machine Learning Approach For Movie Recommendation System **DOI:** [10.1109/ICICCS53718.2022.9788163](https://doi.org/10.1109/ICICCS53718.2022.9788163)

This Paper indroduce the defination of HCB(Hybrid Collaborative-Based ) Machine Learning Approach and it conducts a experiment. The experimental results on movie rating and review rating with movie lens data set shows high result than SVD, KNN and Co-Clustering Mac hine Learning (ML) algorithms.

The movies are recommended using HCB approach which gives good accuracy in comparison with other machine learning approaches like KNN, SVD and co-clustering which gives higher error rate. The data set used is movie lens dataset. The similarity scores are been calculated using cosine similarity and the error rate like NRMSE, MAE, RMSE, RMAE are been calculated. 



## 7. Web-Based Movie Recommendation System using Content-Based Filtering and KNN Algorithm **DOI:** [10.1109/ICITACEE55701.2022.9923974](https://doi.org/10.1109/ICITACEE55701.2022.9923974)

 This system was built based on a content-based movie recommendation system (RS) with Python data analytic tools and the Django web framework. it presented content-based filtering with a cosine similarity algorithm for this system and implemented the KNN algorithm for classification. 

 it calculated the accuracy as a performance evaluation using the KNN classification algorithm resulting from the Cosine Similarity and TF-IDF calculations to verify this system's performance

KNN: The K-Nearest Neighbors (KNN) algorithm is a simple yet powerful supervised learning algorithm used for classification and regression problems. It belongs to the family of instance-based or lazy learning algorithms, where the model is constructed based on the similarity of training examples to the input data.



## 8. Machine Learning approach for Item-basedMovie Recommendation using the most relevant similarity techniques **DOI:** [10.1109/HORA52670.2021.9461381](https://doi.org/10.1109/HORA52670.2021.9461381)

This paper introduces two similarity techniques which are Relevant Jaccard Similarity and Relevant Jaccard Mean Square Distance based on item-based filtering. These two methods increase the exactness of recommendation according to lower counting time. Co-rated items are used for similarity metrics. This model works well in the cold start problems.

The authors introduce a new approach that combines relevance-based item similarity with movie genre and director-based similarity measures. Genre and director attributes are used in their proposed method. This approach works for new items that haven't been rated yet and when all items have the same rating. This method improves recommendation performance on sparse datasets. It also provides better performance in the cold start problem.



