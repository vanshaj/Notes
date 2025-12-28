1. This is the way to optimize the neural network
2. Current our task is the classification problem
3. We start with some random weights and we don't know whether we are doing correct or not. So we need an indicator to tell us whether we are doing things right or not and based on that we can update our weights
4. So we need to define **LOSS FUNCTION**
5. For classification problem **CATEGORICAL CROSS ENTROPY LOSS**. What is idea behind it
	1. So the output we get after applying Softmax activation function will give us some probabilities based on number of neurons we have.
		1. Output of the 1st neuron is the confidence that the input belongs to classA
		2. Output of the 2nd neuron is the confidence that the input belongs to classB
		3. Output of the 3rd neuron is the confidence that the input belongs to classC
	2. Formula $$-\sum Y*\log(Y^`)$$
	3. Negative Summation of True label multiplied by log of Predicted Label

### One Hot Encoding
Is a way of representing the actual output in matrix form . E.g our dataset has 3 classes (Red, Green, Blue). Our dots can come under any 3 classes either red, either blue or either green
Our training data set will have the values for 3 classes and then on new input we have to predict which class our data belongs to . So during training we will One-Hot-Encode our outputs.
If point belongs to Red class then output will be \[ 1,0,0 \]
If point belongs to Green class then output will be \[ 0,1,0 \]
If point belongs to Blue class then output will be \[ 0,0,1 \]

### Calculating Categorical cross entropy Loss
Let's assume our output from neural network which has 3 neurons in last layer outputs 
1. for a input \[  0.7, 0.1, 0.2\] but the point actually belongs to Red Class( One Hot Encoding \[1, 0, 0\])
	So in order to calculate loss 
```Categorical Cross Entropy Loss
=>	-(1*log(0.7) + 0*log(0.1) + 0*log(0.2))
=>  -log(0.7)
=>  -(-0.3566) = 0.3566
```
2. for a input \[  0.5, 0.1, 0.2\] but the point actually belongs to Red Class( One Hot Encoding \[1, 0, 0\])
	So in order to calculate loss 
```Categorical Cross Entropy Loss
=>	-(1*log(0.5) + 0*log(0.1) + 0*log(0.2))
=>  -log(0.5)
=>  -(-0.69) = 0.69
```
3. Hence , more confidence we have less loss will be
4. Also what we saw that incorrect classes doesnot matter at all as their coefficient will be 0.
5. So we are using -ve log of a probability, and probability will always be 0<p<1 , hence our output will always be +ve 

```functionplot
---
title: Categorical Cross Entropy Loss
xLabel: x
yLabel: -ln(x)
bounds: [-10,10,-10,10]
disableZoom: false
grid: true
---
y=-log(x)
```

6. Also we don't want our predicted values to go beyond a certain extent so that when we apply log on it. It will not become total 0 or infinity. 
	1. Why ? because if our predicted value is 0 then log(0) will be infinity
	2. And if predicted value is 1 then log(1) will be 0
	3. So we will keep the values between  $$1e-7 < val < 1 - 1e-7$$
	4. Where $$1e-7 == 0.0000001$$
	5. 