# Social Interest  Recommender system

Team Project for CMPE 256 Large Scale Analytics Course in Summer 2019

The project was developed in google collab a notebook editor based on IPython.

We used Google Colab to make the notebook runnable directly in a browser from any OS (Python code runs in cloud on a virtual machine in a google server), moreover Colab guarantees read and write access to our Google Drive where we stored the datasets in a simple and fast way.

The project description is in CMPE256_Team11_Final_Project.docx and below. The code and the discussion of the experiments are in the notebook CMPE256.ipynb . Dataset files are in the data folder.

# Project description 

Used  WikiMID dataset in the form of tab separated values (TSV)

For every user ui , given his/her Wikipedia-mapped interests, finds a set of CATEGORIES representing a synthesis of the shown preferences. In general, you should have less categories than interests (categories should “synthesize” a user’s main interests).

Note that there is no pairwise correspondence between wikipages and semantic interests. The latter are collectively extracted from the full set of wikipages for each user.

You may use any semantic resource (Wikipedia categories, DBPedia, Babelnet..) and any method you can invent or find in literature 


1. Categories from interests
Extracting categories to syntethize interests using semantic is not a well defined problem, how can we measure the performance of our approach? We should make some assumptions:

Each interest should be semantically related to its category
Categorization should be balanced
Granularity should be appropriate: too many categories are redundant, too few categories weaken the model due to the loss of information.
The balance of the categorization is an easy measure but what about the other two criteria?

Let's see our approach to the problem.

We used precomputed wikipage embeddings as semantic model, these are obtained applying Wor2vec algorithm to reading sessions (sequence of articles visited by a user during a wikipedia session), where articles that tend to be read in close succession have similar representations. Since people usually generate sequences of semantically related articles while reading, these embeddings also capture semantic similarity between articles. Each vector has 100 dimensions.

We select all vectors corresponding to wikipedia articles present in the WikiMID dataset (216.000 out of 1.828.000) and perform a clusterization using MiniBatch K-Means, Minibatch because of the size of the dataset, K-Means because it is more prone to generate balanced clusters and the number of clusters is tunable. However clustering suffers from the curse of dimensionality and 100 dimensions are too much, so we performed at first a dimensionality reduction with PCA, the final number of dimensions is chosen looking at the explained variance, 6 dimensions in our case.

The number of clusters is decided looking both at the silhouette coefficient (look below for more details) and at the semantic range of categories (we do not want too few clusters even if the corresponding silhouette coefficient is high, all the categories would be about music).

The final clusters represents the categories, we named each category looking at the centroid of the cluster, the name follows the standard: Macrocategory (Centroid page name). Where Macrocategory refers to the category of the centroid page in Babelnet (e.g. Music, Literature and Theatre...). The simple Babelnet categorization is not good because led to a heavy unbalanced categorization, for instance Music would include more than 60% of all the pages. With our method instead we have many music categories, distinguished by the centroid page name, e.g. Music (Blind_Melon), Music (Yo_Gotti)... This partition of the categories is meaningful, for instance Music (Blind_Melon) can be associated to USA Rock, while Music (Yo_Gotti) to Rap
