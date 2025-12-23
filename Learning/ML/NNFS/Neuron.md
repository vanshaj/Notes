- Neuron has inputs, weights and bias
	- Inputs x1, x2, x3
	- Weights w1, w2, w3
	- Bias b1
	- $$x_{1}*w_{1} + x_{2}*w_{2} + x_{3}*w_{3} + b$$
```run-python
x1 = [1 ,2 , 3]
w1 = [0.2, 0.8, -0.5]
bias = 2
output = (x1[0]*w1[0] + x1[1]*w1[1] + x1[2]*w1[2] + bias)
print(output)
```

- Layers of the Neurons
	- We have to arrange group of neurons together
	- 4 inputs and 3 neurons
	- Every neuron will receive input from all 4 inputs it will remain same 
		- x1, x2, x3, x4
	- Every neuron will have different weights and bias
		- w11, w12, w13, w14, b1
		- w21, w22, w23, w24, b2
		- w31, w32, w33, w34, b3
- Now lets go one by one
	- For first neuron output will be $$x_{1}*w_{11}+x_{2}*w_{12}+x_{3}*w_{13}+x_{4}*w_{14}+b_{1}$$
	- For second neuron output will be $$x_{1}*w_{21}+x_{2}*w_{22}+x_{3}*w_{23}+x_{4}*w_{24}+b_{2}$$
	- For third neuron output will be $$x_{1}*w_{31}+x_{2}*w_{32}+x_{3}*w_{33}+x_{4}*w_{34}+b_{3}$$
```run-python
x = [1, 2, 3, 2.5]
w = [[0.2, 0.8, -0.5, 1],
[0.5, -0.91, 0.26, -0.5],
[-0.26, -0.27, 0.17, 0.87]]
b = [2, 3, 0.5]
outputs = []
for wt, bs in zip(w, b):
	output = 0
	for in_val, in_wt in zip(x, wt):
		output = output + in_val*in_wt
	output = output + bs
	outputs.append(output)
print(outputs)
```

In Matrix formation above can be written as  $$X*W^T + B$$
