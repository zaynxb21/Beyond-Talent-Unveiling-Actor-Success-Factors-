# Beyond Talent: Unveiling the Hidden Factors Behind an Actor's Success

Data Story : https://epfl-ada.github.io/ada-2024-project-arabsdatascientists2024/

## Abstract

It is safe to say that movies are part of our lives. Who hasn’t dreamed of being the star on the big screen? What if we told you we’ve cracked the code to becoming a successful actor? Sure, talent is essential, but could other factors—like physical attributes, social background, or the roles you play—hold the key to stardom? The film industry is a labyrinth of challenges, where countless dreamers compete, but only a select few rise to fame. In this exploration, we unveil the hidden factors that shape an actor's journey to success. After reading this, ask yourself: Do you have what it takes to shine? Would you dare to take your shot?

## Research Questions

- To what extent does the character played influence the public’s affection?
- Do an actor’s physical attributes, such as height or ethnicity, contribute to their success?
- How does an actor’s social background impact their career success?
- How can an actor’s success be assessed based on box office revenue and public ratings?
- Are actors more famous during their life or after their death?

## Proposed Additional Datasets (if any)

**CelebA :**  This dataset regroups more than 200K images of 10177 unique celebrities each described by 40 binary attributes. CelebA is very valuable for identifying which physical traits (heights, smile, hair color...) contributes to an actor's "success". We contacted the author of the dataset to have access to celebrities' name as they were missing in the public version.

**IMDB :** IMDb is probably the largest database collection related to movies. In the context of our project, it is extremelly useful to analyze the correlation between an actor popularity and box office revenues, movie ratings and reviews. It contains lots of crucial factors - audience feedback, sentiment analysis - which are worth exploring to answer our research question.

**RottenTomatoes :** This website is well known for providing informative reviews on movies, we can use both the audience score and certified critics to decide if a movie is worth being watched. By analyzing the scores from this dataset we gain further insights on how well an actor performs in a given movie.

**Oscars and Awards :** Oscars win and nomination are key indicators of an actor recognition and career achievment. It is the Motion Picture Academy members who get to vote and determine the winner, these professional opinions can change an actor's life and boost its career.

**BoxOfficeMojo :** BoxOfficeMojo is another dataset allowing to assess a movie performance. By looking at the life time gross and the rankings of movies between 1888 and 2016 (what we have in our original dataset) we gain additional success metrics.

**Metacritic :** This dataset aggregates "metascores" and "userscores" to rate a movie, based on the collection of millions of reviews. It allows us to assess how well movies are received by critics but also by the public which helps identify successful movies. It also contains some additionnal information about the movies, which helped us complete our plots for example.

**Webscrapping:**

* Completing missing values in already existign features : As a couple of important columns for our analysis are filled with NaN values. We decided to enrich and complete these columns with webscrapping. The webscrapping was done using a programmable search engine and the creation of our own API. With the help of these elements, we search for whatever the query is (e.g actor's height) and extract the information that corresponds to this query. As the code contains our API key, we will not publish it for security. However, the functions used are in a .py document savedd locally. **CE QUIL Y AVAIT AVANT, A MODIFIER !**
* Incorporating new features : First, we queried Wikidata for attributes like education and occupation, though we ultimately retained only these two from that source. Next, we scraped short biographies from Wikipedia and used sentence embeddings to infer nuanced categories such as socioeconomic background, education level, and early life opportunities. These categories were identified by comparing biography embeddings to representative phrase embeddings and selecting the closest semantic match. We dropped the original education attribute from Wikidata and the occupation attribute due to better, more specific data elsewhere. Finally, we kept all NLP-derived categories from the Wikipedia stage, as they provided more contextual richness.

## Methods

We will employ the methods we discovered in the course to analyze the datasets:

**Data visualization:** Using scatter plots, bar charts, and heatmaps to clearly represent tendencies.

**Correlation analysis:** to identify relationships between actor success and variables like physical attributes, movie genres, or roles.

**Regression Models:** Linear and logistic regression models to assess the impact of quantifiable traits (e.g., box office earnings, age).

**Classification Models:** Learning models to predict actor success based on input variables like physical attributes, roles, and social background. We could use classification or clustering if needed.

**NLP Models :**

* "all-MiniLM-L6-v2" model : (after webscrapping actors biographies we stated earlier) We used this transformer-based sentence embedding model known for its efficiency and strong performance to convert both our curated reference phrases and each actor’s biography into high-dimensional embedding vectors. We chose some categories and wrote their corresponding phrase embeddings and then computed cosine similarities between the biography embedding and each category’s representative phrase embeddings to quantify their semantic alignment.

**Regression:** Linear, Saturated, Regularized and GBR (Gradient Boost regression)

* Linear Regression with smf : linear regression was very helpful to determine which features were significant. By looking at the summary of the regression we could identify the features with p-values < 5%, which had the most impact on the score. It also allowed us to identify the features that we were going to use in the saturated regression. We ended this regression with plot showing confidence intervals for each feature to make sure they were far from 0.
* Saturated Regression : we employed saturated regression, to include interaction terms between the significant features. This approach was particularly valuable for the CelebA dataset, where combinations of features often played a significant role in shaping success. For example, the interaction between "Pale Skin" and "Rosy Cheeks" revealed a synergistic effect that boosted scores, even though these traits had limited individual impact. On the other hand, it didin’t provide much for the social background attributes.
* Regularized Regression Lasso & Ridge : To handle the high dimensionality of celebA we also tested regularized regression. Lasso regression was particularly restrictive in feature selection. It shrinked all our coefficients to zero except for 3 : “Gray hair”, “Attractive” and “Big lips”. On the other hand Ridge was less severe but it impacted large coefficients, to ensure that no individual feature disproportionately influenced the results. The features found with Lasso were top 3 in Ridge, and consistent with the 2 previous regressions.
* Gradient Boost Regression : This method helped us see which features were best for predicting our score. For instance, GBR confirmed the importance of traits like "Bald" and "Gray Hair," which were also identified by all the other techniques as significant predictors. SHAP values visualization gave us insights into which traits drove success to further confirm our results.

**Clustering Models:** K-means, K-modes and GMM (Gaussian Mixture Models)

* K-means & K-modes : First, since the data has mainly categorical features, we used K-modes to cluster our data, which is the adapted version of K-means designed to cluster categorical data. We chose the Huang initialisation and a simple matching dissimilarity as a distance meteric. To determine a suitable number of clusters, we employed the "elbow method" which involves running the clustering with varying numbers of clusters (K) and examining the resulting cost function. We identified an optimal K (K=2) and performed K-modes. However, these results were not very convincing and we rapidly understood that the main challenge is that our data is cathegorical and it is difficult to determine a distance even using dissimilarity. Additionnaly, we couldn't consider all the numerical data, which was a huge data loss. Using hot encoding, we transformed our data to numerical data which allowed us to us the entire datasets columns to perform the clustering.  We scaled the data using StandardScaler and applied K-means. We then evaluated the mean of each cluster to see how the groups behaved in terms of `Score_PCA_2_scaled` that differentiate the successful actors from the unsuccesful. Visualizations then helped interpret the clusters by comparing distributions of attributes and seeing how each cluster performed relative to the chosen success metric.
* GMM : The goal is to identify meaningful clusters of actors based on various attributes. First, we adapted the dataset to our model needs. After trying several cluster counts, a moderate number of clusters (four) was chosen, considering both statistical criteria (like AIC/BIC) and the desire to avoid over-complicating the model. However, when examining these clusters, it became clear that they did not form distinctly interpretable groups. Neither a single attribute, such as Score_PCA_2_scaled, nor a combination of simple numeric or categorical features, created a clear pattern of separation. Visualizations showed substantial overlap among clusters. This suggests that the chosen features and approach did not yield easily distinguishable clusters (the data’s underlying structure may be too complex, or the selected features insufficient)..

**Deep Learnig Models:** MLP (Multi-Layer Perceptron), used with and without CelebA dataset.

* Without CelebA : We use a supervised learning approach to predict the transformed target (Score_PCA_2_scaled) by treating it as a classification task. Initially, the MLP’s performance metrics (accuracy, precision, recall, and F1-score) were modest. The class distribution was skewed, and the model struggled to achieve strong predictive results. After applying a logarithmic transformation to the target, which helped redistribute classes more evenly, the model’s performance improved well (from 35% to 71% test accuracy). MLP demonstrated the potential to extract more meaningful patterns when the target was reshaped.
* With CelebA : In an attempt to refine the model further, CelebA attributes (such as specific facial features) were integrated into the analysis. The hypothesis was that these additional characteristics might provide new, informative signals for predicting the transformed target. However, the incorporation of CelebA attributes did not lead to improved results, we have slightly worse results than the version without CelebA data (drop form 71% to 60% test accuracy). The model may have become more complex without gaining beneficial information, or these attributes may not correlate strongly with the target.

**Combinations:**  We grouped by all possible combinations of features in order to see which group that has a certain combination will have the best success score, which is another way to determine the success factors.

**Network Analysis :**
In this section, we set out to explore the connections and collaborations between actors to better understand what contributes to their success beyond talent. To begin, we filtered the dataset to focus on the top 10% of actors based on their scores (Score_PCA_2_scaled). This allowed us to concentrate our analysis on the most relevant data.

Next, we constructed a collaboration graph using movie IDs, genres, and countries. In this graph, each node represented an actor, and edges connected actors who had worked on the same movie. While this initial graph captured the full dataset, it turned out to be overly complex and challenging to interpret visually.

To simplify the analysis, we narrowed our focus to the largest connected component of the graph—referred to as the largest community. This subset contained the densest network of actor collaborations, making it a natural candidate for deeper exploration. Within this largest community, we applied a community detection algorithm to identify smaller, distinct clusters of actors who frequently collaborated. This step revealed meaningful groupings that were easier to analyze and interpret.

Once the communities were established, we computed various graph statistics to gain insights into the structure and dynamics of the network. These included measures like centrality, degree, density, and clustering coefficients, calculated both for the entire graph and for individual communities. These metrics helped us understand how actors were connected and how influential certain actors or groups were within the network.

Finally, we turned our attention to the top-performing actors—those with scores greater than 7. We identified these high-scoring actors, highlighted them in the graph, and examined which communities they belonged to. This final step provided valuable insights into how collaboration networks might contribute to an actor’s success and which clusters were particularly impactful in fostering such success.

## Limits
- **Physical features** : We have done an analysis to identify the physical attributes that would propel an actor to the top of the rankings. To do so, we used celebA dataset, however it had attributes for only 4% of our data. Thus, we did a subset analysis but we know that this doesn't represent the result we could have obtained with a larger dataset.

  PS : Data is too big to be uploaded on git
