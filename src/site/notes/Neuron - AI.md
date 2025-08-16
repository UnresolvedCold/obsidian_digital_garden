---
{"dg-publish":true,"permalink":"/neuron-ai/","tags":["compilation"]}
---

# Neuron 
A single neuron in code terms is just a block that takes input, multiplies it by a number called weight and add another number called bias and passes it to a filter called activation function. 

$z = a(\sum_{i=1}^{n}(x_i*w_i)+b)$

And the activation function can be any function that generates non-linearity. 
Generating non-linearity means, based on the calculations it can produce a yes or no signal.
The activation function actually acts as a if-else block for a neuron.

## Architecture
The neuron class basically encapsulates, weights, bias and activation function. 

``` java
class Neuron {
   int size;
   double [] weights;
   double bias;
   IActivationFunction activationFunction;

   public Neuron(int size, IActivationFunction activationFunction) {
     this.size = size;
     this.weights = new double[size];
     this.bias = 1.0;
     this.activationFunction = activationFunction;
   }

   public double fordwardPass(int [] input) {
	   double sum = 0.0;
	   for (int i=0; i<size; i++) {
	     sum += weights[i]*input[i];
	   }
	   sum += bias;
	   return activationFunction.activate(sum);
   }
}
```

## Code 
https://github.com/UnresolvedCold/learnaijava/tree/f33c3a1242ef55256186f2b5f8a517b53a96b86c
# Learning in neuron

A neuron leans basically means, changing the weights and bias so that a particular input provides the desired output. 

## 2 steps of learning - forward pass and backward pass
First we provide input to neuron and get the output. This is forward pass. 
This output is compared against the desired output and we penalise the output by a delta number. 

This delta number is what we call loss and is calculated using a loss function ($L$). 
If loss is within a limit we define then we are good with the neuron else we update the neuron's weight and bias to a number which will lead to less loss. 

The updation of weight and bias is done by moving the weight or bias in the direction of less error. 

The direction of less error can be calculated using derivatives of loss function against the variable. In this case, weight and bias both are variable against which we want a direction of movement. 

$direction_b = -\frac{\partial L}{\partial b}$
$direction_w = -\frac{\partial L}{\partial w}$
$w_{new} = w_{old} + direction_{w_{old}}$

To control the amount of movement in the direction, we introduce another variable $\eta$ called the learning rate, which is just multiplied to the direction. 

$w_{new} = w_{old} + \eta * direction_{w_{old}}$

This will update the weight and bias of the neuron and this process is called backward propagation. 

The catch is loss function, $L$ is dependent on desired output and output we produced. 
Desired output is fixed. But the output we produced is dependent on input and the activation function we are using.

Let Z is the desired output and Y be the output we produced. 

$L=f(Z, Y)$
$\partial_w L = \partial_w f(Z,Y) =  \partial_w f(Z,a(WX+b))$
$\partial_b L = \partial_b f(Z,Y) =  \partial_b f(Z,a(WX+b))$

We cannot directly apply derivative in this case as the activation function is encapsulating weight and bias. So we will need to invoke chain rule to make our lives easier. 

Let's represent t as any one of w or b.
$\partial_t L = \partial_a f(Z,Y)*\partial_{WX+b}(a(WX+b))*\partial_t (WX+b)$

The cool thing about the above formula is that the last 2 parts is the property of the neuron and the first part is external. 

## Architecture

Now, we can update our Neuron class to calculate the second part of the formula using the first part. The first part we can pass from external world.  

$$delLoss = \eta * \partial_a f(Z,Y)$$

```java
class Neuron {
   int size;
   double [] weights;
   double bias;
   IActivationFunction activationFunction;

   ... 

   public void backward(double [] input, double [] delLoss) {
     // z = WX + b
     double z = 0.0;  
	 for (int i = 0; i < size; i++) {  
	   z += input[i] * weights[i];  
	 }  
	 z += bias;

    // ∂L/∂z = ∂L/∂y * ∂y/∂z  
    double delL = delLoss * activationFunction.derivative(z);  
  
    // gradients  
    double[] gradWeights = new double[size];  
    for (int i = 0; i < size; i++) {  
      gradWeights[i] = delL * input[i];  
    }  
    double gradBias = delL;  
  
    // update parameters  
    for (int i = 0; i < size; i++) {  
      this.weights[i] -= learningRate * gradWeights[i];  
    }  
    this.bias -= learningRate * gradBias;
   }
}
```

## Code
https://github.com/UnresolvedCold/learnaijava/tree/da0ecd3f244e6731cf7481153e13144ae2c976fd


# 2 linearly connected neurons

When 2 neurons are connected one after the other then the final output will be the output of last layer. 

$y = N_2(N_1(x))$

Here, $N_1$ is the output of first layer and $N_2$ is the output of second layer. 

The loss in this case will be $L(z, y)$. 
And the derivative of this loss will be $\partial_t L(y) = \partial_t L(a_2(a_1(W_1X+b_1))W_2 + b_2)$

This looks complex, let's add variables to make it easier. 

$z_1 = W_1X + b_1$
$a_1=f_1(z_1)$
$z_2 = W_2a_1 + b_2$
$a_2=f_2(z_2)$
t is any variable one of w1, b1, w2, b2

$$
\frac{\partial L}{\partial t} = \frac{\partial L}{\partial a_2}*\frac{\partial a_2}{\partial z_2}*\frac{\partial W_2a_1+b_2}{\partial t}
$$
## Layer 2

This means for layer 2, we can derive the direction of decreasing loss using the below
$$
\frac{\partial L}{\partial W_2} = \frac{\partial L}{\partial a_2}*\frac{\partial a_2}{\partial z_2}*a_1
$$
This basically is multiplication of (loss change wrt final activation), (derivative of activation of layer 2) and input of previous layer

## Layer 1

For layer 1, we have to go further in the chain.
$$
\frac{\partial L}{\partial W_1} = \frac{\partial L}{\partial a_2}*\frac{\partial a_2}{\partial z_2}*\frac{\partial W_2a_1+b_2}{\partial W_1} = \frac{\partial L}{\partial a_2}*\frac{\partial a_2}{\partial z_2}*\frac{\partial z_2}{\partial a_1}*\frac{\partial a_1}{\partial z_1}*\frac{\partial z_1}{\partial W_1}
$$
This can be simplified as below,
$$
\frac{\partial L}{\partial W_1} = \frac{\partial L}{\partial a_2}*\frac{\partial a_2}{\partial z_2}*W_2*\frac{\partial a_1}{\partial z_1}*X
$$
This can be seen in 2 parts, $\frac{\partial L}{\partial a_2}*\frac{\partial a_2}{\partial z_2}$ which is common with layer 2 and $W_2 \partial_{z_1}a_1$ 

OR in summary, layer 2 gradient depends on input from layer 1 and activation function of layer 2.
And layer 1 gradient depends on input to layer 1 and activation of layer 1 and error from layer 2.

> This will give us power to propagate the error from layer 2 to layer 1 during layer 1 calculations. 

```java
public static void main(String[] args) {  
  IActivationFunction identity = new IdentityActivation();  
  
  // Two neurons: N1 takes input x, N2 takes output of N1  
  Neuron n1 = new Neuron(1, identity, 0.01);  
  Neuron n2 = new Neuron(1, identity, 0.01);  
  
  // Training data for y = 2x + 5  
  double[][] inputs = {  
      {1}, {2}, {3}, {4}, {5}  
  };  double[] targets = {  
      7, 9, 11, 13, 15  
  };  
  
  for (int epoch = 0; epoch < 1000; epoch++) {  
    double totalLoss = 0.0;  
  
    for (int i = 0; i < inputs.length; i++) {  
      double[] x = inputs[i];  
      double yTrue = targets[i];  
  
      // Forward pass  
      double out1 = n1.forwardPass(x);  
      double out2 = n2.forwardPass(new double[]{out1});  
  
      // Loss (MSE)  
      double loss = 0.5 * Math.pow(out2 - yTrue, 2);  
      totalLoss += loss;  
  
      // dL/dout2  
      double gradOut2 = out2 - yTrue;  
  
      // Backprop through n2  
      double[] gradToN1 = n2.backwardPass(new double[]{out1}, gradOut2);  
  
      // gradToN1[0] is dL/dout1 for the first neuron  
      n1.backwardPass(x, gradToN1[0]);  
    }  
    if (epoch % 100 == 0) {  
      System.out.println("Epoch " + epoch + ", Loss: " + totalLoss);  
    }  }  
  // Test after training  
  double testX = 10;  
  double out1 = n1.forwardPass(new double[]{testX});  
  double out2 = n2.forwardPass(new double[]{out1});  
  System.out.println("Prediction for x=10: " + out2);  
}

```

## Code
https://github.com/UnresolvedCold/learnaijava/tree/d7b8037af993dc6859ac29fb54c9f4df56c0e567

# How many neurons are enough?
Very experimental question. 
Generally people start with less number of neurons and keep increasing them till the loss is high.
Too many neuron may lead to over fitting.

> 1 neuron means 1 non-linear decision

You can use a single neuron to learn a line.

# How to connect multiple neurons to create a neural network?

## Layer
A layer is a collection of neurons which receives the same input.
