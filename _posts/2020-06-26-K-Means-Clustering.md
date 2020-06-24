---
layout: post
title: K-Means Clustering
---

### Github File
Location of my [.py file](https://github.com/NealWhitlock/CS_DS_Build_Week_1/blob/master/kmeans_cluster.py)


### What is K-Means Clustering?

K-Means Clustering is an unsupervised algorithm for machine learning. In effect, you are looking to group together items that are "near" each other in features. The easiest way to think about this idea is in terms of a 2-D Cartesian graph. For example, in this graph below there are four well-defined clusters with points near each other. K-Means Clustering will iterate through a process of finding a center for those clusters.

<p align="center">
  <img width="721" height="466" src="https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/no_color.png?raw=true">
</p>

### How It Works

There are different ways to initialize a K-Means algorithm. The two most common that I came across in my research were either assigning points to a cluster's centroid at the beginning or to randomly choose a centroid for each cluster among the points given. I chose to use the latter method and randomly pick K points to act as the first centroids of the clusters.

From here, the algorithm checks the distance of each point to each centroid. Whichever centroid is closer to a given point has that point assigned to its cluster. This is done for each data point given. Once all points have been assigned to a cluster the algorithm then calculates the center of all points in the cluster by way of getting the mean for each dimension and assigning that center as the new centroid. 

This process is repeated over and over until the centroids are no longer changed from one iteration to the next. At this point the program breaks and saves the cluster and centroid assignments. 

In itself, that's not so complicated. But there is one problem that comes up based on the initialization. Remember how the centroids were randomly chosen? That can cause some problems where clusters are created that are obviously worse than they otherwise could be to a human observer. Take this clustering for example.

<p align="center">
  <img width="721" height="466" src="https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/A_gif.gif?raw=true">
</p>

Three of the intitial centroids begin in the same cluster. The algorithm goes through the process of assigning points to clusters and moving centroids but that one green cluster has two clearly distinct groups in it while the red and yellow clusters are in the same little group. This is certainly not as good as it could be and it's not like this is a rare occurance in K-Means Clustering. Here are two more instances. 

<p align="center">
  <img width="721" height="466" src="https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/B_gif.gif?raw=true">
</p>

<p align="center">
  <img width="721" height="466" src="https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/C_gif.gif?raw=true">
</p>

And I wasn't even trying to make these. It just happens somewhat frequently.

### Fixing the Process

How can we correct this? Quite simply, actually; we discard the outcomes that result in poor clustering and keep the one that results in the best clustering.

The way I decided to do this punishing in my implementation was to get the mean squared distance from each centroid to each point in its cluster. These values are then saved while the program starts again with all new random initial centroids. The new mean squared distances are then calculated and compared to the prior distances. Since the distances are being squared then the longer the distance is the larger the mean squared distance; this is punishing clusters that are very spread out. The program them saves the centroids that result in the lowest sum in the comparison.

By default the program runs through this process of finding centroids five times but can be set to as many as the user would like. Each time I ran this, though, at least once in those default five runs the clusters were appropriately assigned as such:

<p align="center">
  <img width="721" height="466" src="https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/D_gif.gif?raw=true">
</p>

Even when they went through some odd calculations like this:

<p align="center">
  <img width="721" height="466" src="https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/E_gif.gif?raw=true">
</p>

### How To

To use this program you will need to find/create tabular, numerical data. You then create an instance of the class such as:

`k = KMeans()`

There are some default arguments that you can change in this class. They are:

k_clusters
The number of clusters you want the algorithm to create. Default is 2.

max_iters
The maximum number of iterations that the program will run through in a single instance before quitting. If the data you are using is not resulting in convergence quickly you can increase this number to let it run longer. Default is 50.

n_runs
How many times to loop through creating centroids to look for the best formation of clusters. Default is 5.

If you want to change each of these you can do so like:

`k = KMeans(k_clusters=10, max_iters=100, n_runs=15)`

Once you have initialized the program you can run it by calling the fit method with your data. If you have saved your data points to a variable X that would look like:

`results = k.fit(X)`

Here I have saved the assignments to the variable 'results' for use later in this post.

### Testing

How well does it work, though? It's nice to look at the animated graphs above and say, "That looks wonderful!" But it's not really a great way to approach data. To actually use the centroids formed in the fit method you on new data you can use the predict method on new data. Doing so will then go through the new data points and calculate which cluster they belong to based on the best centroids from fit. This could look like:

`y_pred = k.predict(X_test)`

In order to check this process out I saved the first fifty point from the dataset I passed into the fit method as X_test. Predict calculates exlcusively based on the location of the centroids so passing in data points from the initial dataset doesn't cause a conflict but is instead a perfect way to test the validity of the predict call.

With a different dataset with 6 clusters that I ran through this algorithm the first fifty points of X were assigned clusters in the following way (the first point was put into cluster 5, second point in cluster 4...):

[0. 0. 3. 3. 4. 4. 4. 2. 2. 3. 1. 0. 1. 1. 3. 4. 0. 2. 0. 0. 5. 1. 2. 1. 0. 0. 5. 1. 3. 4. 2. 3. 5. 1. 1. 3. 0. 5. 0. 3. 5. 1. 2. 1. 5. 0. 5. 3. 0. 1.]

I then ran those fifty points through the predict method and they were assigned the following clusters:

[0. 0. 3. 3. 4. 4. 4. 2. 2. 3. 1. 0. 1. 1. 3. 4. 0. 2. 0. 0. 5. 1. 2. 1. 0. 0. 5. 1. 3. 4. 2. 3. 5. 1. 1. 3. 0. 5. 0. 3. 5. 1. 2. 1. 5. 0. 5. 3. 0. 1.]

Based on the best centroids found in fit the predict method returned the same clustering for the test data points. 

However, when I import KMeans from scikit-learn and fit that model on my dataset of X and run a prediction on X_test I get the following returns:

[5 3 0 0 4 4 4 4 1 0 2 3 2 2 0 4 3 1 5 3 1 2 1 2 3 5 1 2 0 4 4 0 1 2 2 0 5 1 5 0 1 2 1 2 1 3 1 0 5 2]

I wouldn't expect the cluster numbers to match up (after all, there would only be a 1 in 6! way of that happening). But the structure should still be the same and it's not. This requires investigating.
