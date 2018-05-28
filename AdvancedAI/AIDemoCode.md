---
export_on_save:
 html: true
---

@import "/blog_head.md"

# AI代码集

## ANN Code

> 丰言丰语：对于机器学习的代码，不需要对真的理解到数学层面及其代码实现，只需要知道对应的函数的用处，并且在需要的时候能够修改即可！但是也不能完全不理解处理流程，而只会使用函数接口，这样会影响后期的代码修改。

### 加载数据集

#### 数据集下载

MNIST 数据集是一个手写数字的图片集，后面的很多代码都通过这个数据集进行实验。该数据集可以通过 [mnist-original.tar.bz2](https://github.com/foolishflyfox/CsLearnNote/raw/master/dataset/mnist-original.tar.bz2) 链接下载。这个下载下来的是压缩文件，可以需要通过命令 `tar -xjvf mnist-original.tar.bz2` 将其解压出一个 mat 文件。

#### 数据加载

```python
>>> import scipy.io
# 路径即为刚才解压缩的 mat 文件所在文件路径
>>> mnist = scipy.io.loadmat("datasets/mnist-original.mat")
>>> mnist
{'__globals__': [],
 '__header__': b'MATLAB 5.0 MAT-file Platform: posix, Created on: Tue Feb 13 12:51:29 2018',
 '__version__': '1.0',
 'data': array([[0, 0, 0, ..., 0, 0, 0],
        [0, 0, 0, ..., 0, 0, 0],
        [0, 0, 0, ..., 0, 0, 0],
        ..., 
        [0, 0, 0, ..., 0, 0, 0],
        [0, 0, 0, ..., 0, 0, 0],
        [0, 0, 0, ..., 0, 0, 0]], dtype=uint8),
 'label': array([[ 0.,  0.,  0., ...,  9.,  9., 9]]),
 'mldata_descr_ordering': array([[array(['label'],
       dtype='<U5'),
         array(['data'],
       dtype='<U4')]], dtype=object)}
```
分离出其中的输入和输出，并分为训练集(50000个)、验证集(10000个)、测试集(10000)个：
```python
mnist_x = mnist['data']
mnist_y = mnist['label']
mnist_set = []
for i in range(70000):
    # 将 y 向量化，如 6.0 变为 
    # array([[0],[0],[0],[0],[0],[0],[1],[0],[0],[0]])
    y_vector = np.zeros((10, 1))
    y_vector[int(mnist_y[0][i])] = 1
    mnist_set.append((mnist_x[:, i].reshape(-1,1)/256, y_vector))
random.shuffle(mnist_set)
# t1_set 为训练集，t2_set 为验证集
train_set = mnist_set[:60000]
t1_set, t2_set = train_set[:50000], train_set[50000:]
# test_set 为测试集
test_set = mnist_set[60000:]
```

### ANN 代码实现

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
    def foreward(self, a, convert_f=None):
        for w,b in zip(self.weights, self.biases):
            a = self.activate_fun(np.dot(w, a) + b)
        return convert_f(a) if convert_f else a
    
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
```
测试代码：
```python
>>> net = Network([784, 30, 10])
>>> net.train(t1_set, 30, 10, 3.0, test_data=t2_set, notify=True)
Epoch 0 : 9061:10000
Epoch 1 : 9233:10000
Epoch 2 : 9292:10000
Epoch 3 : 9339:10000
Epoch 4 : 9358:10000
Epoch 5 : 9363:10000
Epoch 6 : 9364:10000
Epoch 7 : 9404:10000
Epoch 8 : 9383:10000
Epoch 9 : 9393:10000
Epoch 10 : 9411:10000
Epoch 11 : 9417:10000
Epoch 12 : 9425:10000
Epoch 13 : 9427:10000
Epoch 14 : 9429:10000
Epoch 15 : 9434:10000
Epoch 16 : 9435:10000
Epoch 17 : 9439:10000
Epoch 18 : 9419:10000
Epoch 19 : 9433:10000
Epoch 20 : 9412:10000
Epoch 21 : 9431:10000
Epoch 22 : 9439:10000
Epoch 23 : 9434:10000
Epoch 24 : 9439:10000
Epoch 25 : 9430:10000
Epoch 26 : 9425:10000
Epoch 27 : 9439:10000
Epoch 28 : 9427:10000
Epoch 29 : 9433:10000
```
使用含10000个样本的 `test_set` 测试识别率：
```python
def vector2num(y_vector):
    return np.argmax(y_vector)
print(sum(net.foreward(test_set[i][0], vector2num)==vector2num(test_set[i][1])
            for i in range(len(test_set))))
```
结果为`9406`，和预期的训练结果类似。


经验：
- 一般情况下，使用更多的神经元，能够帮助我们得到更好的结果
- 调试神经网络不是琐碎的，就像常规编程那样，它是一门艺术，更普遍地是，我们需要启发式的方法来选择好的超参数和好的结构

当隐藏层的数量为2层或者更多时，这样的网络称为深度神经网络。 80年代和90年代的研究人员尝试了使用随机梯度下降和方向传播来训练深度网络，不幸的是，除了一些特殊的结构，他们并没有取得很好的效果。虽然网络能够学习，但是学习速度非常缓慢，不适合在实际中使用。



## 支持向量机

```python
from sklearn import svm

clf = svm.SVC()
trainX = np.vstack(x.flatten() for x,_ in train_set)
trainY = np.array([np.argmax(y) for _,y in train_set])

# 可能需要训练较长时间
clf.fit(trainX, trainY)

test_x = np.vstack(x.flatten() for x,_ in test_set)
test_y = np.array([np.argmax(y) for _,y in test_set])
predict_y = clf.predict(test_x)

print(sum(predict_y==test_y))
```
结果为：`9431`

可以看出，除了速度比神经网络要慢一些外，性能和神经网络相差无几。

通过对 SVM 参数的优化，可以把性能提高到 98.5% 的精确度。目前精心设计的神经网络胜过任何其他解决 MNIST 的技术，包括 SVM，可以达到 99.8% 的准确率。

**复杂的算法 ≤ 简单的学习算法 + 好的训练数据**

## 使用 scikit-learn 提供的神经网络

```python
from sklearn.neural_network import MLPClassifier

clf = MLPClassifier((30,), activation="logistic", 
                    solver="sgd", learning_rate_init=3)

clf.fit(trainX, trainY)
# test_x、test_y 在 SVM 节中产生
predict_y = clf.predict(test_x)
print(sum(predict_y==test_y))
```
结果为：`9205`


