---
layout: post
title:      "Wound up, Wined up. "
date:       2019-08-16 05:02:26 +0000
permalink:  wound_up_wined_up
---

### A neurotic approach to tuning some hyperparamaters of a neural network



For our penultimate project for Flatiron School, we were instructed to use deep learning methods on a dataset of our choosing. I've come to learn that the best datasets to work with, at least for personal projects, are those that deal with information that is interesting to you as an individual. So, what? you might ask, piques the interest of a girl who's 30th birthday is a mere 9 days away? Well, I still love to drink. Of course. But I've graduated to the classy stuff. Let's talk about wine baby. 


After cleaning and exploring a dataset scraped from WineEnthusiast (*that's me!*) Magazine from the week of the 11/24/17 the final dataset that I will be training and testing on looks like this:

![](https://github.com/FHyder/Module4Project/blob/master/Screen%20Shot%202019-08-15%20at%2011.32.26%20PM.png)

That's right, 98,057 observations consisting of information on points assigned by WineEnthusiast, sentiment analysis scores on reviews, country, province, and grapes of each of the wines. Now, a deep learning neural network consists of three or more layers including the input and output layer. I like the "or more" clause so I went ahead and started with a 4 layer neural network: input, output, and two hidden layers. There are several paramaters that can be tuned when fitting a neural network however I focused on the following

1) Number of hidden layers
2) Activation functions
3) Optimizers
4) Epochs (*kinda, sorta. more on this later*)


For regression problems, the recommended activation functions are "relu" and "linear" functions and the recommended optimizers are the Adam. I fully planned on using every combination of these activation functions/optimizers pairs but before we get to that lets rewind a little. First of all, how do you build a neural network?

```
    model = Sequential() 
    model.add(Dense(24, activation = activation1, input_shape = (122,)))
    model.add(Dense(16, activation= activation2))
    model.add(Dense(1))
    model.compile(loss='mse',
              optimizer= opt,
              metrics=['mse']) ##MSE
    callbacks = [EarlyStopping(monitor='val_mean_squared_error', patience=4),
             ModelCheckpoint(filepath='best_model.h5', monitor='val_mean_squared_error', save_best_only=True)]
    history = model.fit(X_train, 
              y_train,
              epochs = 150,
              batch_size = 5,
              verbose = 0,
              validation_data= (X_test, y_test), callbacks = callbacks)
	
```

Step 1) Instantiate Sequential model
Step 2 to infinity) add your layers, nodes, and activation functions until you've added as many as you lie
Step infinity +1) model.compile(your loss function, your optimizer, your metric are necessary more on callbacks in a sec
Step infinity +2)  fit your model to your training data, define epochs, batch size, verbose = 0 if you don't want a breakdown of how every epoch performed, and validation_data set as your testing data

I said we'd talk about epochs and callbacks and I wasn't lying. Defining a large number of epochs allows your model to iterate and reiterate and reiterate again in an attempt to achieve a low MSE it can get *exhausting* but is good practice. Just like it's namesake, the brain, a neural network needs practice to achieve ideal scores. Also like a brain, our neural network sometimes reaches a point where it can perform no better. That's where callbacks come in. Using early stopping, in the code above I am monitoring val_mean_squared_error a.k.a my testing MSE. We expect the testing MSE to decrease with each epoch. When there is an increase from one epoch's val-mse to the next the callback above will take note and end the model if the next 3 epochs are still higher than the original one here's an example:

Epoch1 : 23, Epoch2: 22, Epoch3: 24 *triggers early stopping*, Epoch4: 30, Epoch5: 31, Epoch6: 28 *lower than previous epoch but still higher than epoch3*, Epoch7: 25......AND STOP

The EarlyStopping method has a patience set to 4 in the example above. You can set this to any number and this will indicate how many epochs to observe past an increase in the metric your monitoring. For instance, if you set patience to 2, the example above would've stopped after Epoch 5.

Now back to our optimizers and activation functions. So two optimizers **x** two hidden layers  **x**  two possible activation functions = 8 possible combinations. Can I type all these out? Yes, of course, but coders are lazy by nature so of course I created a function.

```
def twoLayerNeuralNet(activation1, activation2, opt, X_train, y_train, X_test, y_test):
    model = Sequential()
    model.add(Dense(24, activation = activation1, input_shape = (122,)))
    model.add(Dense(16, activation= activation2))
    model.add(Dense(1))
    model.compile(loss='mse',
              optimizer= opt,
              metrics=['mse']) ##MSE
    callbacks = [EarlyStopping(monitor='val_mean_squared_error', patience=4),
             ModelCheckpoint(filepath='best_model.h5', monitor='val_mean_squared_error', save_best_only=True)]
    history = model.fit(X_train, 
              y_train,
              epochs = 150,
              batch_size = 5,
              verbose = 0,
              validation_data= (X_test, y_test), callbacks = callbacks)
    history_dict = history.history
    training_predictions = model.predict(X_train)
    y_pred = model.predict(X_test)
    history_dict['y_pred'] = y_pred
    history_dict['training_prediction'] = training_predictions
    return history_dict
```

After running all of my neural networks using said function I created ANOTHER function to return the lowest MSE's reported by each model and then graphed them to identify the *winner* (Neural Networks work hard and deserve recognition)

```
def min_value(list):
    min = list[0]
    for i in list:
        if i < min:
            min = i
    return min

```

![](https://github.com/FHyder/Module4Project/blob/master/Screen%20Shot%202019-08-16%20at%2012.41.19%20AM.png)

My best performing model had an RMS optimizer and a Relu-Linear combination. To check for improvements I added another layer in between these two layers. Kept the optimizer the same but experimented by building a model with an added layer with an activation function of 'relu' and another model with an added layer that had a 'linear' activation function.  

```
opt=optimizers.RMSprop(0.0001)
threeLayer = Sequential()
threeLayer.add((Dense(24, activation = 'relu', input_shape = (122,))))
threeLayer.add(Dense(16, activation = 'relu'))
threeLayer.add(Dense(16, activation = 'linear'))
threeLayer.add(Dense(1))
threeLayer.compile(loss='mse',
              optimizer= opt,
              metrics=['mse'])
callbacks = [EarlyStopping(monitor='val_mean_squared_error', patience=4),
             ModelCheckpoint(filepath='best_model.h5', monitor='val_mean_squared_error', save_best_only=True)]
history = threeLayer.fit(X_train, 
              y_train,
              epochs = 150,
              batch_size = 5,
              validation_data= (X_test, y_test), callbacks = callbacks, verbose = 0)
history1_dict = history.history
predictions = threeLayer.predict(X_train)
y_pred = threeLayer.predict(X_test)
history1_dict['y_pred'] = y_pred
history1_dict['tpredictions'] = predictions

mse.append(min_value(history1_dict['val_mean_squared_error']))
model_name.append('R-Relu-Relu-Lin')
training_mse.append(min_value(history1_dict['mean_squared_error']))
```

```
threeLayer2 = Sequential()
threeLayer2.add((Dense(24, activation = 'relu', input_shape = (122,))))
threeLayer2.add(Dense(16, activation = 'linear'))
threeLayer2.add(Dense(16, activation = 'linear'))
threeLayer2.add(Dense(1))
threeLayer2.compile(loss='mse',
              optimizer= opt,
              metrics=['mse'])
callbacks = [EarlyStopping(monitor='val_mean_squared_error', patience=4),
             ModelCheckpoint(filepath='best_model.h5', monitor='val_mean_squared_error', save_best_only=True)]
history = threeLayer2.fit(X_train, 
              y_train,
              epochs = 150,
              batch_size = 5,
              validation_data= (X_test, y_test), callbacks = callbacks, verbose = 0)
history2_dict = history.history
y_pred = threeLayer2.predict(X_test)
predictions = threeLayer2.predict(X_train)
history2_dict['y_pred'] = y_pred
history2_dict['tpredictions'] = predictions
mse.append(min_value(history2_dict['val_mean_squared_error']))
model_name.append('R-Relu-Linear-Linear')
training_mse.append(min_value(history2_dict['mean_squared_error']))

```

so did adding layers improve model performance?
DRUMROLL PLEASE......

![](https://github.com/FHyder/Module4Project/blob/master/Screen%20Shot%202019-08-16%20at%2012.56.36%20AM.png)

Nah not really. I could've tuned more paramater but with aj RMSE of roughly 13 across a dataset of over 98,000 observations, I decided I've built enough models. Also, be aware: running all 10 neural networks took approximately 3 hours. Despite EarlyStopping! Looking forward to future projects and experimenting with alternative paramaters. 


