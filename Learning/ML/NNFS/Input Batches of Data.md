1. Assume we have 3 neurons in one layer
2. What if rather than 1 input we have multiple batches input of data, usually there are multiple set of measurements are done. In this case 3 batches of input
	```
	[1 2 3 2.5 -- first batch
	2 5 -1 2.0 -- second batch
	-1.5 2.7 3.3 -0.8 -- third batch]
	```
3. For Each batch of input each neuron will have different set of weights 
	1. Each neuron will have 4 weights $$w_{1} w_{2} w_{3} w_{4}$$
	2. Each neuron will have 1 bias $$b_{1}$$
4. For Each batch of input each neuron will give one output per neuron. 
	1. As we have 3 neurons in one layer so each layer will give 3 output per input
	2. And we have 3 inputs

	Resultant is 3 * 3  matrix which is basically $$X*W^T + B$$
	`` X - Input Matrix , W - Weight Matrix , B - Bias``
	```
	[
	]
	```