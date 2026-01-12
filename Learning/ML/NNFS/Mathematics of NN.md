### Derivative
1. First in order to identify  the correct weights and biases we have to identify the impact of weights and biases on the loss function, then we can change our weights and biases accordingly
2. We have feature inputs and based on that we have an ouput.
3. Similarly we have set of weights , based on which we have calculated loss . We need to identify , how much change in weights will cause how much change in loss function $$\Delta x \to \Delta y$$
4. Derivative will tell us slope of a function at any point. This will exactly tell us how much change in x at a specific point will cause the change in y $$f(x)^` = \frac{d f }{d x} $$
5. Derivative of combined function is derivative of individual functions $$\frac{d (f + g)}{d x} = \frac{d f}{d x} + \frac{d g}{d x}$$
### Partial derivative
1. Now rather than only being a function of x let's assume function is of (x,y,z) then how can we calculate the derivative of the function
2. So we will calculate partial derivative of the function with respect to all of the variables and that will give us the derivative $$f(x,y,z)^` = \frac{\partial f(x,y,z)}{\partial x}+\frac{\partial f(x,y,z)}{\partial y} + \frac{\partial f(x,y,z)}{\partial z}$$
### Gradient
1.  Tells us the direction in which the function steeps the most. 
2. It is a vector of all possible partial derivative of the function
