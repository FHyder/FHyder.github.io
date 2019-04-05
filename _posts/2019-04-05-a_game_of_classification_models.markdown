---
layout: post
title:      "A Game of Classification Models"
date:       2019-04-05 12:05:36 +0000
permalink:  a_game_of_classification_models
---


The content of your blog post goes here.

Blog Post Must-Haves
Your blog post should clearly explain your process and results, including:
	•	An explanation of the problem you're trying to solve and the dataset you choose for it
	•	Well documented examples of code and visualizations (when appropriate)
NOTE: This blog post is your way of showcasing the work you've done on this project--chances are it will soon be read by a recruiter or hiring manager! Take the time to make sure that you craft your story well, and clearly explain your process and findings in a way that clearly shows both your technical expertise and your ability to communicate your results!

With the season premier of my favorite show around the corner, I thought for this project it would be fun to work with a dataset about Game of Thrones. The show is known to kill off characters quite suddenly and brutally so in this notebook I chose to build a classification model that can predict whether a character is alive.

The dataset I’m using presents different details of 1,946 characters in the show. As with any dataset the first thing I did after loading the set was check the info:

charac_pred.info()

RangeIndex: 1946 entries, 0 to 1945
Data columns (total 33 columns):
S.No                 1946 non-null int64
actual               1946 non-null int64
pred                 1946 non-null int64
alive                1946 non-null float64
plod                 1946 non-null float64
name                 1946 non-null object
title                938 non-null object
male                 1946 non-null int64
culture              677 non-null object
dateOfBirth          433 non-null float64
DateoFdeath          444 non-null float64
mother               21 non-null object
father               26 non-null object
heir                 23 non-null object
house                1519 non-null object
spouse               276 non-null object
book1                1946 non-null int64
book2                1946 non-null int64
book3                1946 non-null int64
book4                1946 non-null int64
book5                1946 non-null int64
isAliveMother        21 non-null float64
isAliveFather        26 non-null float64
isAliveHeir          23 non-null float64
isAliveSpouse        276 non-null float64
isMarried            1946 non-null int64
isNoble              1946 non-null int64
age                  433 non-null float64
numDeadRelations     1946 non-null int64
boolDeadRelations    1946 non-null int64
isPopular            1946 non-null int64
popularity           1946 non-null float64
isAlive              1946 non-null int64
dtypes: float64(10), int64(15), object(8)


Off the bat I observed that title, culture, dateofbirth, dateofdeath, mother, father, heir, spouse, isAliveMother, isAliveFather, isliveHeir, isAliveSpouse, and age were missing way too many variables. These were dropped. On the website where I got the data from there’s no description for the variable S.no, actual, and pred so there’s were dropped as well. Finally alive was dropped for it represented the same information as isAlive.
This left us with: 'male', 'house', 'book1', 'book2', 'book3', 'book4', 'book5', 'isMarried', 'isNoble', 'numDeadRelations', 'isPopular', 'popularity’, isAlive’ as the variables to work with. Fortunately, this set did not need much cleaning, the only series that really needed one-hot encoding was ‘house.’ After dropping the null values I checked how many unique houses were represented in the dataset, it was 327! I didn’t want to create 327 so instead I painstakingly divided the houses into 13 different categories and then created categorical dummies for them.
 

The first classification model I used was a Decision Tree. 
1) First, I identified the target and the features: 
      X = got.drop(['isAlive'], axis = 1)
       y = got['isAlive']

2) Then I created a split/train model with a split of 30/70 


X_train, X_test , y_train,y_test = train_test_split(X, y, test_size = 0.3, random_state = 100)

3) Instantiate a decision tree, fit it to your train data, make predictions. 

clf= DecisionTreeClassifier(criterion='entropy')
clf.fit(X_train,y_train) 
y_pred = clf.predict(X_test) #build my tree

I checked the accuracy and F-1 score to each of my models, this one produced 78% accuracy and F1 score of 86%.

Classification models often require some fine tuning. I implemented a GridSearch to help determine which paramaters I should use for my decision tree with the following code:

1) Set paramater_grid
param_grid = {
    'criterion': ['gini', 'entropy'],
    'max_depth': [None, 2, 3, 4, 5, 6],
    'min_samples_split': [2, 5, 10],
    'min_samples_leaf': [1, 2, 3, 4, 5, 6],
    
    
}

2) Use GridSearch with these paramaters:

gs_tree = GridSearchCV(clf, param_grid, cv = 3)
gs_tree.fit(X_train, y_train)
print('The best paramaters are ', gs_tree.best_params_)
 
 
 Using the paramaters that my GridSearch recommended, increased the accuracy and precision of my model while also cutting down on how many splits were made in the tree before a prediction was made.
 
 
 
 The second model I used was a randomforest model. Random forest introduce movre variability in how our models learn by making several decision trees that incorporate different features for each tree and then uses the cumulative observations of each tree to make a prediction.
 
 Creating a random forest is as easy as the following code:
 forest = RandomForestClassifier(n_estimators=100)
forest.fit(X_train, y_train)
forest_pred = forest.predict(X_test)

Again, ther was an improvement in both the accuracy and the F-1 score compared to the Decision Trees but in order to improve efficiency I ran anothe grid search and tuned my paramaters. 

Now, I'm currently building my models using 24 different predictors. I thought it would be more efficient to create a model where you don't need to know as much about any given character in order to make a prediction. So first I built a function to plot feature importances :

def plot_feature_importances(model, data_train):
    n_features = data_train.shape[1]
    plt.figure(figsize=(8,8))
    plt.barh(range(n_features), model.feature_importances_, align='center') 
    plt.yticks(np.arange(n_features), data_train.columns.values) 
    plt.xlabel("Feature importance")
    plt.ylabel("Feature")
		
		and used it to drop the bottom five features. Unfortunately, this didn't increase the score on either of the metrics I was using.
		
		
Finally, I tried the popular XGBboost classification method. I reiterated the steps that I used with my first two models and used GridSearch to tune, the plot_feature_importance method to drop unimportant features, and ultimately produced a model that used 11 fewer features than the first too to make predictions at the same level of accuracy at a fraction of the time.

I fed the specifications of three dfferent characters to my model and got a correct prediction everytime! This was the model I ultimately chose.

Overall, this project showed me how fun it can be to work with a dataset that piques your interest. Obviously this will not alway be the case in the real world, but data mining of any sort is a reiterative process. Finding a set that is interesting to you is the best way to learn new concepts while making this process a lot less laborious.


 


