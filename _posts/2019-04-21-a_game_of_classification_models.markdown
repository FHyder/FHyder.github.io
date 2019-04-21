---
layout: post
title:      "A Game of Classification Models"
date:       2019-04-21 11:40:23 +0000
permalink:  a_game_of_classification_models
---



With the new season of my favorite show, Game of Thrones,  making it's debut after a two year hiatus, I thought for this project it would be fun to work with a dataset about it. The show is known to kill off characters  suddenly and brutally, so in this notebook I chose to build a classification model that can predict whether a character is alive.

The dataset I’m using presents observations about 1,946 characters in the show. As with any dataset the first thing I did was load the dataset into a dataframe and then checked the head and the info

``character_pred = pd.read_csv('character-predictions.csv')``
``char_pred.head()``



``charc_pred.info()``


Off the bat I observed that title, culture, dateofbirth, dateofdeath, mother, father, heir, spouse, isAliveMother, isAliveFather, isliveHeir, isAliveSpouse, and age were missing way too many variables. These were dropped. On the website where I got the data from there’s no description for the variable S.no, actual, and pred so there’s were dropped as well. Finally alive was dropped for it represented the same information as isAlive. Popularity was also dropped because it is a score that is generated based on the opinion of the dataset's author. There's no clear rubric for this number and thereby it is arbritrary.This left us with: ‘male’, ‘house’, ‘book1’, ‘book2’, ‘book3’, ‘book4’, ‘book5’, ‘isMarried’, ‘isNoble’, ‘numDeadRelations’, ‘isPopular’, and isAlive’ as the variables to work with. 
``got = charac_pred.drop(['isAliveMother', 'isAliveFather','isAliveHeir', 'isAliveSpouse', 'age', 'S.No', 'actual', 'pred','plod' ,'title'  ], axis = 1)``
``got.drop(['mother', 'father', 'heir', 'spouse', 'culture', 'dateOfBirth', 'DateoFdeath', 'alive', 'boolDeadRelations', 'name', 'popularity'], axis = 1, inplace = True)``

Fortunately, this set did not need much cleaning, the only series that really needed one-hot encoding was ‘house.’ After dropping the null values I checked how many unique houses were represented in the dataset, it was 327! I didn’t want to create 327 so instead I painstakingly divided the houses into 13 different categories and then created categorical dummies for each of them.

The first classification model I used was a **Decision Tree**. 
1) First, I identified the target and the features:  
``X = got.drop([‘isAlive’], axis = 1)`` 
``y = got[‘isAlive’]``

2) Then I created a split/train model with a split of 30/70

``X_train, X_test , y_train,y_test = train_test_split(X, y, test_size = 0.3, random_state = 100)``

3) Instantiate a decision tree, fit it to your train data, make predictions.

``clf= DecisionTreeClassifier(criterion=’entropy’) ``
``clf.fit(X_train,y_train) y_pred = clf.predict(X_test) ``

I checked the accuracy and F-1 score to each of my models, this one produced 78% accuracy and F1 score of 86%.

Classification models often require some fine tuning. I implemented a GridSearch to help determine which paramaters I should use for my decision tree with the following code:

1) Set paramater_grid:
``param_grid = 
{ ‘criterion’: [‘gini’, ‘entropy’], ‘max_depth’: [None, 2, 3, 4, 5, 6], ‘min_samples_split’: [2, 5, 10], ‘min_samples_leaf’: [1, 2, 3, 4, 5, 6],
}``

2) Use GridSearch with these paramaters:

``gs_tree = GridSearchCV(clf, param_grid, cv = 3)``
``gs_tree.fit(X_train, y_train) ``
``print(‘The best paramaters are ‘, gs_tree.best_params_)``

***The best paramaters are  {'criterion': 'entropy', 'maxdepth': 2, 'minsamplesleaf': 1, 'minsamplessplit': 2}***


Using the paramaters that my GridSearch recommended, increased the accuracy to 80% and precision of my model to 89% while also cutting down on how many splits were made in the tree before a prediction was made.

The second model I used was a randomforest model. Random forest introduce more variability in how our models learn by making several decision trees that incorporate different features for each tree and then uses the cumulative observations of each tree to make a prediction.

Creating a random forest is as easy as the following code: 
``forest = RandomForestClassifier(n_estimators=100)``
``forest.fit(X_train, y_train)``
``forest_pred = forest.predict(X_test)``

There wasn't too much variablity in accuracy (80%) and precision (88%) compared to the Decision Trees but in order to improve efficiency I ran anothe grid search and tuned my paramaters. With tuned paramaters accuracy went up to 84% and 91%

Now, I’m currently building my models using 23 different predictors. I thought it would be more efficient to create a model where you don’t need to know as much about any given character in order to make a prediction. So first I built a function to plot feature importances :

``def plot_feature_importances(model, data_train): 
n_features = data_train.shape[1] 
plt.figure(figsize=(8,8)) 
plt.barh(range(n_features), model.feature_importances_, align=’center’) plt.yticks(np.arange(n_features), data_train.columns.values) 
plt.xlabel(“Feature importance”) 
plt.ylabel(“Feature”) ``


![Image did not load](https://raw.githubusercontent.com/FHyder/dsc-3-final-project-online-ds-pt-100118/master/Screen%20Shot%202019-04-21%20at%207.26.38%20AM.png)

I ran a model with five fewer features and the Accuracy went up to 86% while the F1Score stayed at 91% which is very promising
	

Finally, I tried the popular XGBboost classification method. I reiterated the steps that I used with my first two models and used GridSearch to tune, the plot_feature_importance method to drop unimportant features, and ultimately produced a model that used 11 fewer features than the first two to make predictions at the similar levels of accuracy(82%) and precision (89%). Then I tuned the model with a GridSearch which kept models performing at the same accuracy (82%) and precision(89%) and finally I used the feature importance function to identify and drop 11 features from the model which decreased accuracy to 81% while keeping precision to about 89. 

All of my models performed at high levels of accuracy and precision but the one that out-performed them all was the Random Forest with five fewer features. I fed the specifications of three dfferent characters to my model and got a correct prediction everytime! This was the model I ultimately chose.


Overall, this project showed me how fun it can be to work with a dataset that piques your interest. Obviously this will not alway be the case in the real world, but data mining of any sort is a reiterative process. Finding a set that is interesting to you is the best way to learn new concepts while making this process a lot less laborious.
