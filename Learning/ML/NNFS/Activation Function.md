Q. Why do we need it? 
Ans. To implement non linearity

Before passing the output of one layer directly to other layer, we first pass it to another layer that is called **ACTIVATION FUNCTION** so that it will add non linearity to the output and then it is passed to the next layer. Role of this activation function to add non linearity so that it can capture any non linearity in the data we have.

Activation Layer needs to be added at each hidden layer . If we don't have the activation function our Hidden Layers will not be able to capture the structure of the underlying data
### ReLU 
It is very powerful, $$y = max(0,x)$$

```functionplot
---
title: ReLU
xLabel: X
yLabel: Y
bounds: [-10,10,-10,10]
disableZoom: false
grid: true
---
y=max(0,x)
```


```functionplot
---
title: ReLU(X-2)
xLabel: X
yLabel: Y
bounds: [-10,10,-10,10]
disableZoom: false
grid: true
---
y=max(0,x-2)
```

```functionplot
---
title: -ReLU(X-2)
xLabel: X
yLabel: Y
bounds: [-10,10,-10,10]
disableZoom: false
grid: true
---
y=-max(0,x-2)
```

```functionplot
---
title: 2-ReLU(X-2)
xLabel: X
yLabel: Y
bounds: [-10,10,-10,10]
disableZoom: false
grid: true
---
y=2-max(0,x-2)
```

We can see with above functions, that ReLU is almost able to capture all non-linearity in data . We can stack these ReLU's together and get the non-linear function

But ReLU is not a good activation function for the classification tasks. Why? Because in our neural network we are calculating probability distribution at the output that will tell us to which category the input belongs e.g Dog/Cat
But in ReLU if there is any layer output which is -ve ReLU will make it 0 , no matter if it's -9 or -9000 thereby loss of information. But for our PDF that matters. So we should use some other approach like [[SoftMax Activation Function]]

### Sigmoid $$y = \frac{1}{1+e^-x}$$

```functionplot
---
title: Sigmoid
xLabel: X
yLabel: Y
bounds: [-10,10,-10,10]
disableZoom: false
grid: true
---
y = 1/(1+exp(-x))
```
### Linear (NOT GOOD AT ALL)$$y = x$$

```functionplot
---
title: Linear
xLabel: X
yLabel: Y
bounds: [-10,10,-10,10]
disableZoom: false
grid: true
---
y=x
```

### [[SoftMax Activation Function]]
Best for multiclass classification, even though the starting layers of our neural network wants to use ReLU but at the end we need SoftMax Activation 
It keeps the values between (0,1) and it makes sure all the values are some sort of probabilities

``                      Softmax = Exponentiation + Normalization               ``
$$\sigma(\mathbf{z})_i = \frac{e^{z_i}}{\sum_j e^{z_j}}$$
    All the output values will be positive
    Denominator will always be greater than powder so value will lie between 0-1
    If we sum all the values it will always be equal to 1 

Let's assume we will have 100 batch of inputs and each input will have 3 features, neurons will be 3 so the output will be 100 * 3 matrix. For each input we will have 3 values 

Now for each row of output we will apply softmax activation function and that will be softmax

*One SAFE thing we want to do to become the exponent value too large , we will subtract max value of the row output from all values to sort of shift the values*