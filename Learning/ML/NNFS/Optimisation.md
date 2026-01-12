1. Why Optimisation?
	1. Question arises why do we even need optimisation, if we think currently we don't know what weights and biases we should have for each neuron in each layer. We are just randomly initiating those weights and biases and calculating the output with the help of forward pass. 
	2. After forward pass we calculated loss on our training data set , with help of our predicted values and actual values
	3. But our goal is to minimise that loss and get the weights where the loss is minimal
	4. That is why we need optimisation to identify the correct weights and bias with minimal losses
2. Let's try to create a random algorithm for this optimization
	1. Randomly starts with a weight and bias and move randomly
	2. Now if we observe that loss is decreased then we have to pick the next weight and bias in the same area near that point
	3. We keep on doing that till we reach the minima
	4. But if the loss increases we will not update the weights and bias