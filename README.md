# 1. Movie-Recommendation


https://cloud.google.com/solutions/machine-learning/recommendation-system-tensorflow-overview



## 2.Models
### 2.1 Collaborative filtering 
-- Collaborative filtering relies only on observed user behavior to make recommendations — no profile data or content access is necessary: Users with shared preferences are likely to respond in the same way to the same items. -> Combining these basic observations allows a recommendation engine to function without needing to determine the precise nature of the shared user preferences.   
-- For example, suppose User 1 has viewed items A, B, C, D, E, and F. User 2 has viewed items A, B, D, E and F, but not C. Because both users viewed five of the same six items, it's likely that they share some basic preferences. User 1 liked item C, and it's probable that User 2 would also like item C if the user were aware of its existence.   

### 2.2 Matrix factorization for collaborative filtering
-- Rating matrix: Suppose you have a matrix consisting of user IDs and their interactions with your products. Each row corresponds to a unique user, and each column corresponds to an item.Each entry in the matrix captures a user's rating or preference for a single item. The rating could be explicit, directly generated by user feedback, or it could be implicit, based on user purchases or time spent interacting with an article or video. This matrix is normally large and sparse.  
<img src="https://cloud.google.com/solutions/machine-learning/images/recommendation-system-tensorflow-movielens-rating-matrix.svg"
     alt="Rating Matrix"
     style="float: left; margin-right: 10px;" />

-- Latent factor: The matrix factorization method assumes that there is a set of attributes common to all items, with items differing in the degree to which they express these attributes. Furthermore, the matrix factorization method assumes that each user has their own expression for each of these attributes, independent of the items. In this way, a user's item rating can be approximated by summing the user's strength for each attribute weighted by the degree to which the item expresses this attribute. Intuitively, it's easy to see that these hypothetical latent factors actually exist.       

-- The core is to mathematically transform the ratings matrix to account for latent factor.


#### 2.2.1 Low-Rank Matrix Factorization and WALS method for matrix factorization. 

##### Low-Rank Matrix Factorization
- pick an arbitrary number k of latent factors .   
- for a set of users U of size u and items I of size i,factorize the large rating matrix R into two much smaller matrices X (the "row factor") and Y (the "column factor"). Matrix X has dimension u × k, and Y has dimension k × i. In linear algebra this is called a low-rank approximation. You can view this process as compressing the sparse information in R into the much lower dimensional spaces u × k and k × i .  
- The matrix R', obtained when the low-rank matrices X and Y are multiplied, represents an approximation of R, which can be seen as the prediction results. So the loss function measuring the accuracy of the approximation can be defined by over all users and items, suming the squared difference between the approximate rating and the actual rating . 
- It's common practice to add regularization terms to this loss function to help prevent overfitting. Adding L2 regularization terms for both row and column factors gives the following:regularization constant will be one of the model’s hyperparameters to be addressed . 

<img src="https://cloud.google.com/solutions/machine-learning/images/recommendation-system-tensorflow-row-and-column-factors.svg"
     alt="Latent Matrix"
     style="float: left; margin-right: 10px;" />

##### Weighted alternating least squares (WALS) method

-- Alternating least squares method (ALS) -  The alternating least squares method of matrix factorization is an iterative method for determining the optimal factors X and Y that best approximate R. In each iteration, one of the row or column factors is held fixed and the other is computed by minimizing the loss function with respect to the other factor. For example, with column factors fixed, take the derivative of the loss function with respect to the row factors and set it to 0 will derive the new row factors **(notes)**. Alternating row and column factors, the iterative process is repeated until convergence, which typically occurs within a small (< 20) number of iterations even for very large matrices consisting of tens of millions of rows or columns.    
-- Weighted alternating least squares (WALS) method - The weighted version of the loss function for the algorithm introduces different weights for the zero, or unobserved, entries and the non-zero entries in the matrix. The weight differs according to
the sum of the number of nonzero entries for column i **(notes)**. The weight is scaled by the sum of the nonzero entries in a row to normalize the weight for users who have rated a different number of items. The function f may be linear, or one that weights the observed ratings to account for certain distribution. This type of weighting allows for a more flexible model of the user's preferences and produces better empirical results than the unweighted version.    


##### Implementation
WALS is included in the `contrib.factorization` package of the TensorFlow code base, and is used to factorize a large matrix of user and item ratings.

#### 2.2.2 SVD

##### SVD

##### Implementation
scipy package has standard SVD package (`scipy.linalg.svd`).


#### 2.2.3 Stochastic Gradient Descent





## 3. Data
MovieLens
-- Ratings in the MovieLens dataset range from 1 to 5. Empty rating entries have value 0, meaning that a given user hasn't rated the item.

## 4. Implementation
Following the original tutorial using a GCE VM and bash script to orchestrate: https://cloud.google.com/solutions/machine-learning/recommendation-system-tensorflow-create-model .  



## 4. Result
Was able to have test RMSE as low as 0.98 as shown in `ml_job_job_id_wals_ml_tune_sample_logs_testRMSE_0-1` log files. Model exports (factors learnt) and logs folders are included in the repo as well and can be looked up by job_id.


