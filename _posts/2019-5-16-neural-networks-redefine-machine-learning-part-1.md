---
layout: post
title: Neural Networks Redefine Machine Learning Part 1
tags:
  - machine learning
---
In this series of posts, I want to share my understanding & practices of how neural networks and deep learning powered by modern tools (e.g. Tensorflow) redefine machine learning.

By "redefine", I mean neural networks not only can solve the same problems better on small size of datasets than classical statistics based machine learning algorithms can do, but also can deal with huge (e.g. millions of images) and very complex (e.g. thousands of features) datasets, and they enable solving many complex problems machine cannot handle very well before, such as image classification/generation, speech recognition, and language translation, etc.

In part 1 of this series, I'd like to implement some classical statistics based algorithms and some simple neural networks for solving several common machine learning problems. And let's see if we could intuitively make some judgements on which approach rocks.

## Problem 1: Auto Insurance in Sweden (Linear Regression)

In this problem, we use a simple but real dataset - "Auto Insurance in Sweden" to demonstrate linear regression.

This simple dataset contains the number value pairs of the total payment for all the claims in thousands of Swedish Kronor (y) given the total number of claims (x).

You can download and view the dataset from this URL:
https://www.math.muni.cz/~kolacek/docs/frvs/M7222/data/AutoInsurSweden.txt


https://machinelearningmastery.com/implement-simple-linear-regression-scratch-python/


https://www.tensorflow.org/tutorials/keras/basic_regression