---
layout: post
title: Tensorflow 2.0 in Action Part 1 - Linear Regression on Small Datasets
tags:
  - machine learning
---
In this series of posts, I want to share my machine learning study practices of neural networks and deep learning with Tensorflow 2.0. I'd like to solve several common machine learning problems with both classical statistics-based algorithms and neural networks. And let's see if we could intuitively make some judgments on the pros and cons of each side.

## Problem 1: Predict Auto Insurance in Sweden (Linear Regression on Small Datasets)

For solving this problem, we will use a real but simple dataset - "Auto Insurance in Sweden" to demonstrate linear regression on small datasets.

This dataset contains the number pairs of the total payment for all the claims in thousands of Swedish Kronor (y) given the total number of claims (x). It is simple enough that it only contains one feature x, to predict a single value y, so we could easily visualize it in a 2D axis.

You can download and view the raw dataset from this [URL](
https://www.math.muni.cz/~kolacek/docs/frvs/M7222/data/AutoInsurSweden.txt).

_Reminder: For running the demo code, you need to have Python 3.7+, Tensorflow 2.0, numpy, pandas and sklearn libraries installed._

We first write some code to format the dataset:

``` python
import numpy as np
import pandas as pd
import io
import requests

# download the dataset
url="https://www.math.muni.cz/~kolacek/docs/frvs/M7222/data/AutoInsurSweden.txt"
s=requests.get(url).content

# read the dataset as csv and covert it to numpy arrays
c=pd.read_csv(io.StringIO(s.decode('utf-8')), skiprows=10, sep='\t', dtype={'X': float, 'Y': str})
a=c.to_numpy()
X = a[:,0:1].astype('float')
y = np.array([float(v.replace(',','.')) for v in a[:,1]])
```

Let's visualize the full dataset since it is small enough:

![](./assets/images/part1_raw.png)

Usually, when doing regression to predict a value, we need to first assume the model type. For example, is it more likely to be linear or polynomial? Coz the only thing we're doing in training a machine learning model is to fit the parameters of a predefined model. For this dataset, according to the scatter drawing, we can say a linear model will work well. 

With a classical statistics-based algorithm, this means we will fit parameter w and b for model: y=wX+b.

With sklearn library, we could fit a classical model in only two lines of code. And let's plot the prediction values of all the X with this model. Not surprisely, it is a line in the 2D axis. Looks perfect!

``` python
# fit a classical model
from sklearn.linear_model import LinearRegression
reg = LinearRegression().fit(X, y)

# draw the classical regression model
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
ax.set_xlabel('x')
ax.set_ylabel('y')
ax.scatter(X, y,  color='k')
ax.plot(X, reg.predict(X),color='g')
plt.show()
```

![](./assets/images/part1_classic.png)


Now, let's try to solve the same problem with neural networks. First, let's try with a shallow neural network, with only one hidden Dense layer of 8 nerons.

``` python
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, Dropout

# construct a shallow neural network
model = Sequential()
model.add(Dense(8))
model.add(Dense(1))
model.compile(loss='mean_squared_error', optimizer='adam')

# standardize the dataset and train the model
model.fit(X/100, y/100, epochs=200)

# Plot neural network model
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
ax.set_xlabel('x')
ax.set_ylabel('y')
ax.scatter(X, y,  color='k')
ax.plot(X, reg.predict(X),color='g')
ax.plot(X, model.predict(X/100.0)*100,color='b')
plt.show()
```

After 200 epochs of training, we get a line which is not as fit as the classical one:

![](./assets/images/part1_nn_shallow_200.png)

But if we increase the epochs to 500, we could get an exactly same perfect line. The blue line almost overlaps with the green line:

``` python
# the same shallow neural network but increase the epochs to 500
model = Sequential()
model.add(Dense(8))
model.add(Dense(1))
model.compile(loss='mean_squared_error', optimizer='adam')
model.fit(X/100, y/100, epochs=500)

# Plot neural network model
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
ax.set_xlabel('x')
ax.set_ylabel('y')
ax.scatter(X, y,  color='k')
ax.plot(X, reg.predict(X),color='g')
ax.plot(X, model.predict(X/100.0)*100,color='b')
plt.show()
```

![](./assets/images/part1_nn_shallow_500.png)

Well, although it takes longer time, but even such a shallow neural network did the job. You might wonder, what if we increase the number of neurons in the hidden layer or add some more hidden layers?

Let's try to increase the number of neurons in the hidden layer from 8 to 128.

``` python
# increase the number of neurons in hidden layer to 128
model = Sequential()
model.add(Dense(128))
model.add(Dense(1))
model.compile(loss='mean_squared_error', optimizer='adam')
model.fit(X/100, y/100, epochs=200)

# Plot neural network model
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
ax.set_xlabel('x')
ax.set_ylabel('y')
ax.scatter(X, y,  color='k')
ax.plot(X, reg.predict(X),color='g')
ax.plot(X, model.predict(X/100.0)*100,color='b')
plt.show()
```

![](./assets/images/part1_nn_shallow_128_200.png)

Well, we can see, this time, with only 200 epochs, we get a perfect fit line.

How about increasing the number of hidden layers instead? Let's use 3 hidden Dense layers, each having 8 neurons.

``` python
# increase the number of hidden layers to 3
model = Sequential()
model.add(Dense(8))
model.add(Dense(8))
model.add(Dense(8))
model.add(Dense(1))
model.compile(loss='mean_squared_error', optimizer='adam')
model.fit(X/100, y/100, epochs=100)

# Plot neural network model
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
ax.set_xlabel('x')
ax.set_ylabel('y')
ax.scatter(X, y,  color='k')
ax.plot(X, reg.predict(X),color='g')
ax.plot(X, model.predict(X/100.0)*100,color='b')
plt.show()
```

![](./assets/images/part1_nn_shallow_8x3_100.png)


Awesome! this time, only with 100 epochs, we get almost the same result.

So, what's our conclusion after these tests?

- Neural networks can do the same job for linear regression tasks;
- Even a simple shallow neural network could fit although it takes longer time to converge;
- Increase the number of neurons in a hidden layer or increase the number of hidden layers could enhance the model;
- A classical statistics-based model takes less time to fit on small dataset.

### Further thinking:

Since a well-trained neural network can predict almost the same results as the classical model, can we simply write down the model as a Maths function, something like y=wX+b? 

The answer is YES as long as there are no activation functions being used in any layers of the model. Actually, if we construct a neural network with only one neuron, the output y exactly equals to wX+b. If you try to train this simplest neural network, you still can get the same prediction results, although it took 4000 epochs to converge on my test machine.

``` python
# the simplest neural network
model = Sequential()
model.add(Dense(1))
model.compile(loss='mean_squared_error', optimizer='adam')
model.fit(X/100, y/100, epochs=4000)

# Plot neural network model
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
ax.set_xlabel('x')
ax.set_ylabel('y')
ax.scatter(X, y,  color='k')
ax.plot(X, reg.predict(X),color='g')
ax.plot(X, model.predict(X/100.0)*100,color='b')
plt.show()
```

![](./assets/images/part1_nn_shallow_1_4000.png)

So, why does a neural network with more neurons or more Dense layers mean? It just means a linear combination of multiple simple y=wX+b models. In this simple dataset, because we only have one feature in X, no matter how many neurons and Dense layers we use, the Maths representation of the model can be written as y=w'X+b', where w' and b' are linear combinations of w and b parameter values of all the neurons in the model. Apparently, it is still a linear model, that's why the plot of the converged model is always the same line in the 2D axis.

The last question: why a neural network with more neurons or Dense layers takes less time to converge? Well, we could understand this as kind of we are doing parallel training of a single neuron. It is not exactly that a 2-neuron model reduces half of the training time from a 1-neuron model, coz the convergence of the model also related to the training data, how you initialize the parameters, the optimizer you use, etc. But provided that you use the best practice to set these so-called hyperparameters, more or less, more neurons or Dense layers reduces training time when you have enough hardware resource (CPU, GPU, memory, etc) to parallel the training.
