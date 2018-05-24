---
export_on_save:
 html: true
---

@import "/blog_head.md"


# AI代码集

## ANN Code

> 丰言丰语：对于机器学习的代码，不需要对真的理解到数学层面及其代码实现，只需要知道对应的函数的用处，并且在需要的时候能够修改即可！但是也不能完全不理解处理流程，而只会使用函数接口，这样会影响后期的代码修改。

```python
class Network(object):
    @classmethod
    def sigmoid(cls, z):
        return 1.0/(1.0+np.exp(-z))
    @classmethod
    def cost_derivative(cls, output_activations, y):
        return (output_activations-y)
    @classmethod
    def sigmoid_prime(cls, z):
        sigmoid_z = cls.sigmoid(z)
        return sigmoid_z*(1-sigmoid_z)
    
    def __init__(self, sizes, activate_fun=None,
                 activate_prime=None, cost_derivative=None):
        self.num_layers = len(sizes)
        self.sizes = sizes
        self.activate_fun = activate_fun
        self.activate_prime = activate_prime
        self.cost_derivative = cost_derivative
        if self.activate_fun==None:
            self.activate_fun = type(self).sigmoid
        if self.cost_derivative==None:
            self.cost_derivative = type(self).cost_derivative
        if self.activate_prime==None:
            self.activate_prime = type(self).sigmoid_prime
        self.biases = [np.random.randn(i, 1) for i in sizes[1:]]
        self.weights = [np.random.randn(sizes[i], sizes[i-1]) 
                         for i in range(1, len(sizes))]
    def foreward(self, a):
        for w,b in zip(self.weights, self.biases):
            a = self.activate_fun(np.dot(w, a) + b)
        return a
    
    def train(self, train_data, epochs, mini_batch_size, eta,
            test_data=None, notify=False):
        if test_data : n_test = len(test_data)
        n = len(train_data)
        for j in range(epochs):
            np.random.shuffle(train_data)
            mini_batches = [train_data[i:i+mini_batch_size] 
                            for i in range(0,len(train_data),mini_batch_size)]
            for mini_batch in mini_batches:
                self.update_mini_batch(mini_batch, eta)
            if notify:
                if test_data:
                    print(f"Epoch {j} : {self.evaluate(test_data)}:{n_test}")
                else:
                    print(f"Epoch {j} complete.")

    def update_mini_batch(self, mini_batch, eta):
        nabla_b = [np.zeros(b.shape) for b in self.biases]
        nabla_w = [np.zeros(w.shape) for w in self.weights]
        for x,y in mini_batch:
            delta_nabla_b, delta_nabla_w = self.backprop(x, y)
            nabla_b = [nb+dnb for nb, dnb in zip(nabla_b, delta_nabla_b)]
            nabla_w = [nw+dnw for nw, dnw in zip(nabla_w, delta_nabla_w)]
        self.weights = [w - (eta/len(mini_batch))*nw
                         for w, nw in zip(self.weights, nabla_w)]
        self.biases = [b + (eta/len(mini_batch))*nb
                       for b, nb in zip(self.biases, nabla_b)]
        
    def evaluate(self, test_data):
        test_result = 0
        for x, y in test_data:
            test_result += int(np.argmax(y)==np.argmax(self.foreward(x)))
        return test_result
    
    def backprop(self, x, y):
        nabla_b = [np.zeros(b.shape) for b in self.biases]
        nabla_w = [np.zeros(w.shape) for w in self.weights] 
        # feedforward
        activation = x
        activations = [x]
        zs = []
        for b, w in zip(self.biases, self.weights):
            z = np.dot(w, activation)+b
            zs.append(z)
            activation = self.sigmoid(z)
            activations.append(activation)
        # backward pass
        delta = self.cost_derivative(activations[-1], y) * self.activate_prime(zs[-1])
        nabla_b[-1] = delta
        nabla_w[-1] = np.dot(delta, activations[-2].transpose())
        for l in range(2, self.num_layers):
            z = zs[-l]
            sp = self.activate_prime(z)
            delta = np.dot(self.weights[-l+1].transpose(), delta) * sp
            nabla_b[-l] = delta
            nabla_w[-l] = np.dot(delta, activations[-l-1].transpose())
        return (nabla_b, nabla_w)
    
net = Network([784, 30, 10])
net.train(t1_set, 30, 10, 3.0, test_data=t2_set, notify=True)
```

经验：
- 一般情况下，使用更多的神经元，能够帮助我们得到更好的结果
- 调试神经网络不是琐碎的，就像常规编程那样，它是一门艺术，更普遍地是，我们需要启发式的方法来选择好的超参数和好的结构

