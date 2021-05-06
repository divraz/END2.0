# What is a neural network neuron?
A layer in a neural network consists of a parameterizable number of neurons. A neural network consists of multiple layers. A neuron consists of a function f(x1, x2, ..., xn), a sigmoid function which uses f as input and gives a binary output and a weight factor which is multiplied with with the sigmoid function and determines how much this neuron is considered for the output of the layer. Each neuron is a mathematical operation that takes it’s input, multiplies it by it’s weights and then passes the sum through the activation function to the other neurons.

# What is the use of the learning rate?
The learning rate is a configurable hyperparameter used in the training of neural networks that has a small positive value, often in the range between 0.0 and 1.0.
The learning rate controls how quickly the model is adapted to the problem.

# How are weights initialized?
Weights are initialized randomly when we start training. Although, there are certain algorithms followed for initialization of weights and randomness is set to a range.

Some examples are:
Orthogonal: Initializer that generates a random orthogonal matrix.
Identity: Initializer that generates the identity matrix.
lecun_uniform: LeCun uniform initializer.
glorot_normal: Glorot normal initializer, also called Xavier normal initializer.
glorot_uniform: Glorot uniform initializer, also called Xavier uniform initializer.
he_normal: He normal initializer.
lecun_normal: LeCun normal initializer.
he_uniform: He uniform variance scaling initializer.

# What is "loss" in a neural network?
Loss is nothing but a prediction error of Neural Net. And the method to calculate the loss is called Loss Function. In simple words, the Loss is used to calculate the gradients. And gradients are used to update the weights of the Neural Net.

# What is the "chain rule" in gradient flow?
The chain rule can also be expressed in Leibniz's notation as follows: If a variable z depends on the variable y, which itself depends on the variable x, so that y and z are dependent variables, then z, via the intermediate variable of y, depends on x as well.
