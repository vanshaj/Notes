We know that we have batches of input , and for each input in first layer we have n number of neurons ```
```Input
X = [
1, 2, 3, 4
4, 5, 6, 7
5, 8, 9, 1
]
```
Each neuron will have set of weights for each input in one set
```Neurons
Neuron 1 = [
0.2, 0.8, -0.5, 1
]
Neuron 2 = [
0.5, 0.6, 2.6, 1.1
]
Neuron 3 = [
0.3, 1.1, 0.2, -0.5
]

As matrix
[
0.2, 0.8, -0.5, 1
0.5, 0.6, 2.6, 1.1
0.3, 1.1, 0.2, -0.5
]
```
We will have bias too for each neuron
```Bias
Bias1 = 1
Bias2 = 2
Bias3 = 2

As Matrix
[1,
2,
2
]
```
We can calculate the forward pass using $$O_{1} = X_{1}*W_{1}^T + B_{1}$$
Now this is one layer , there is another layer too and the output of the first layer will be used as the input of the second layer $$O_{2}=O_{1}*W_{2}^T + B_{2}$$
This will keep on continuing, e.g if we have 50 layers $$O_{50}=O_{49}*W_{50}^T+B_{50}$$
