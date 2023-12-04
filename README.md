# FLATIRON SCHOOL PHASE 3 PROJECT
*Angelo Turri*

## Stakeholder & Problem
A charity organization has a list of Tanzanian water pumps. These pumps can either be:
- entirely functional, 
- functional with some defects in need of repair, 
- or totally non-functional. 

This organization wants to **fix as many of these pumps as it can**. However, they have **limited funds**. To make the most of these funds, they need to be as efficient as possible – this means dispatching **only what is necessary** to each waterpoint to get the job done. In this hypothetical scenario, non functional water pumps require significantly more resources to repair than functional-needs-repair water pumps.

It is our job to use our available data to make predictions about the waterpoints that this charity organization gave us. The organization wants us to remember that non-functional waterpoints cost more to fix than functional-needs-repair waterpoints. We need to distinguish between them to the best of our ability.

## Data Origin & Description
The data was taken from **[drivendata.org](https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/data/).**

We used 7 numeric and 9 categorical variables in our models. Descriptions of any of these variables can be found **below**. Descriptions are also provided **[on the competition website] (https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/page/25/).**

NUMERIC FEATURES:
- amount_tsh: Amount of water available to each waterpoint
- gps_height: Altitude of the well
- longitude: GPS coordinate
- latitude: GPS coordinate
- num_private: number of private waterpoints available to the owner
- population: Population around the well
- construction_year: The year each waterpoint was constructed


CATEGORICAL FEATURES:
- lga (Geographic location): 124 categories. 
- public_meeting: 3 categories. 
- extraction_type (The kind of extraction the waterpoint uses): 18 categories.  
- management (How the waterpoint is managed): 12 categories. 
- payment (How people pay for water at the waterpoint): 7 categories. 
- water_quality (The quality of the water): 8 categories. 
- quantity (The quantity of water each waterpoint provides): 5 categories. 
- source (The source of the water): 10 categories. 
- waterpoint_type (The kind of waterpoint): 7 categories. 

## Methods Justification & Value to Stakeholder
Our current project centers around a ternary classifier, **status_group**, which categorizes a waterpoint into one of three levels of functionality. We will therefore be using a variety of classification models on our data.

Furthermore, we will be taking an **iterative modeling approach**, meaning we will be starting with a basic model and making decisions from there according to our chosen model metrics.

This project yields a model that can generate predictions for all of the water wells that the charity asked us to classify, as well as some further recommendations for overall allocation of resources.
    
## Limitations

- The size of the dataset (roughly 60,000 records) is not optimal, limiting what our models will be able to gather.
- We did not do any feature engineering. There was already an abundance of variables, both numeric and categorical, and most of our time was dedicated to handling the existing variables.
- Our dataset suffered from significant class imbalances, hampering our model's ability to make good predictions in one category, "functional needs repair."
![Target variable distribution](/figures/counts_1.png)

## Data Cleaning

**All visualizations in this README were made on the cleaned dataset.**

- Models perform better without any null values or collinearity in the dataset. So we eliminated null values and any collinear variables.
    - We eliminated collinearity by looking for correlations among the categorical variables and amongst the numeric variables.
    - Correlations between categorical variables were determined through the chi-squared test and its accompanying effect size statistic, Cramer's V.
    - Any effect size between categorical variables above 0.7 meant that all but one of the correlated variables had to be eliminated.
    - The one we'd choose to keep was the one that correlated most strongly with the target variable, status_group.
    - We eliminated 15 of these colinear categorical variables.
    - Correlations between numeric variables were determined through Pearson's correlation coefficient. No numeric variables were eliminated.
- Furthermore, some of the categorical variables had vastly different categories between datasets. For instance, certain categorical variables had thousands of different categories in another dataset. We cannot use these variables in our model.
    - We eliminated 7 of these inconsistent categorical variables.
- We eliminated variables that did not correlate with the target variable whatsoever.
    - We eliminated two of these meaningless categorical variables: an ID column and a column with ony a single value in all 60,000 records.
- We one-hot encoded categorical variables and standardized numeric data to make it easier to use and interpret.
- We performed an 80-20 train-test-split on the data.

## Modeling

**All visualizations in this README were made on the cleaned dataset.**

I used an iterative modeling technique – that is, I started with a basic model and made adjustments from there. My first model was a Logistic Regression estimator with no hyperparameter adjustments on the original dataaset. My final model was an XG-Boost estimator with 6,860 additional generated instances of the 'functional needs repair' class.

Here are the classification reports for each model:
![ROC curves](/figures/reports.png)

At the end of the notebook, I used several visualizations and metrics to compare these models:

- One-vs-rest ROC curves. ROC curves attempt to hug the top left corner of the x-y axis. The more they hug it, the better your model is. An ROC curve is meant for a binary classifier, and we used a ternary classifier, so we had to turn our ternary classifier into three binary classifiers, and generate ROC curves for each of them. Each binary classifier used one category as a reference, and the other two were collapsed.
- Each ROC curve for our final model was better than that of the intitial model.
![ROC curves](/figures/roc.png)

- F1 scores. An F1 score is a balanced average of precision and recall. To illustrate, let's take a specific category in our target variable, "functional." Precision for this category is the percentage of times our model predicted "functional" that were actually correct. Recall for this category is the number of "functional" wells that the model correctly identified as functional. You want good recall and precision, but one does not guarantee the other. A high F1 score means you had good recall and precision, which is why I use it as a metric.
- Each F1 score for our final model was better than that of the initial model.
![F1 scores](/figures/f1_square.png)

- Percentage of non functional wells incorrectly classified as functional. This type of error is particularly bad, since it leaves communities without water. This goes against everything the charity is trying to accomplish, so I try to minimize this kind of error.
- This error was reduced from 15% in the initial model to 10% in the final model.
![Bad error](/figures/error.png)

- Feature importances. I wanted to create visualizations for some important variables. Whether or not a variable is "important" is determined by how important the model thought it was. Feature importances reveal this.
- I picked variables from these feature importances that I could create good, meaningful visualizations out of.
![Feature importances](/figures/feature_importances.png)


## Model predictions
My model predictions were entered on [this competition in datadriven.org](https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/leaderboard/?page=99). I sit at 4,944th place place among 15,945 participants.


## Recommendations

First and foremost, we generated predictions for the set of water wells that the charity organization needed predictions for. Beyond these predictions, we compiled a list of further recommendations and insights. We use accompanying visualizations.

We suggest that the charity organization prioritize wells that are older, since non-functionl and functional-needs-repair wells tend to be older than functional wells.
![Construction year histogram](/figures/year_square.png)

We suggest that the charity organization prioritize wells that have less water available to them, since these wells are more likely to be non functional or in need of repair.
![Model metric visualizations](/figures/amount_tsh.png)

We suggest that the charity organization consult a heatmap of where non functional and functional-needs-repair wells are concentrated to better allocate their resources.
![Model metric visualizations](/figures/heatmap_square.png)

Finally, we suggest that the charity organization prioritize non functional over functional-needs-repair wells. Despite making improvements in predicting functional-needs-repair wells, we were unable to achieve satisfactory accuracy in this category. Our best model only idenfitied 43% of all wells in this category, and when the model predicted such a well, it was only correct only 38% of the time.

This suggests that the category is ill-defined, and a well in "need of repair" could be almost totally fine, or almost completely broken and just barely functional.

## Repository Structure
- **[data](https://github.com/Jellohub/phase2_project/tree/master/data)**: folder containing data files.
    - X_test.csv: the set of wells the charity organization wants us to categorize.
    - X_test_preprocessed.csv: the same set of wells, preprocessed to allow our model to make predictions on it.
    - X_train.csv, y_train.csv: the datasets we use to train our models.
    - predictions.csv: our predictions for the charity organization.
- **[figures](https://github.com/Jellohub/phase_3_project/tree/master/figures)**: folder containing all visualizations used in the project notebook and presentation
- **[models](https://github.com/Jellohub/phase_3_project/tree/master/models)**: folder containing our pickled models
- **[tanzania_polygon](https://github.com/Jellohub/phase_3_project/tree/master/tanzania_polygon)**: folder containing data files for Tanzania's border shape – only used for a couple visualizations
- **[project notebook](https://github.com/Jellohub/phase_3_project/blob/master/notebook.ipynb)**: project notebook containing all data imports, preprocessing, analyses, models, and visualizations
- **[project notebook pdf](https://github.com/Jellohub/phase_3_project/blob/master/notebook.pdf)**: pdf version of project notebook
- **[presentation pdf](https://github.com/Jellohub/phase_3_project/blob/master/presentation.pdf)**: pdf file with all presentation slides
- **[model predictions notebook](https://github.com/Jellohub/phase_3_project/blob/master/model_predictions.ipynb)**: easily executed notebook generating predictions for charity organization's list of water wells; makes use of pickled models and exported preprocessed data to avoid time-consuming model training