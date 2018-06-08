---
export_on_save:
 html: true
---

# 神经网络实验

**姓名：丰华彬** &nbsp;&nbsp;&nbsp;&nbsp;**学号：SA17011135**

## 实验描述

根据用户特征判断该用户是否患有冠状动脉疾病，可通过 <http://archive.ics.uci.edu/ml/datasets/Z-Alizadeh+Sani#> 访问该任务的描述。

结果有两种情况：正常 和 患病。

用户特征共有 55 个：'Age', 'Weight', 'Length', 'Sex', 'BMI', 'DM', 'HTN', 'Current Smoker','EX-Smoker', 'FH', 'Obesity', 'CRF', 'CVA', 'Airway disease','Thyroid Disease', 'CHF', 'DLP', 'BP', 'PR', 'Edema','Weak Peripheral Pulse', 'Lung rales', 'Systolic Murmur','Diastolic Murmur', 'Typical Chest Pain', 'Dyspnea', 'Function Class','Atypical', 'Nonanginal', 'Exertional CP', 'LowTH Ang', 'Q Wave','St Elevation', 'St Depression', 'Tinversion', 'LVH','Poor R Progression', 'BBB', 'FBS', 'CR', 'TG', 'LDL', 'HDL', 'BUN','ESR', 'HB', 'K', 'Na', 'WBC', 'Lymph', 'Neut', 'PLT', 'EF-TTE','Region RWMA', 'VHD'。

数据集中共包含有303个实例。

## 数据预处理

### 加载数据集

从 UCI 网站上所下载的是一个 **xlsx** 文件 。我们可以通过 `pandas` 的 `read_excel` 函数加载该文件。

```python
import numpy as np
import pandas as pd

data = pd.read_excel("./Z-AlizadehSaniDataset.xlsx")
```

### 判断数据集是否有缺失值

```python
print(sum(data.isnull().values.flatten()))
```
输出结果为：`0`，表明数据集中不包含缺失值。

### 处理文本型数据

因为神经网络的输入需要是数字，所以我们需要将数据集中的文本数据转换为数字，文本型数据有如下几种：

- `Sex`：性别，有 'Fmale' 和 'Male' 两种；
- `Obesity`：是否肥胖，有 'Y' 和 'N' 两种；
- `CRF`：有 'Y' 和 'N' 两种；
- `CVA`：是否患有中风，有 'Y' 和 'N' 两种；
- `Airway disease`：是否患有奇怪疾病，有 'Y' 和 'N' 两种；
- `Thyroid Disease`：是否患有甲状腺疾病，有 'Y' 和 'N' 两种；
- `CHF`：是否患有心力衰竭，有 'Y' 和 'N' 两种；
- `DLP`：有 'Y' 和 'N' 两种；
- `Weak Peripheral Pulse`：脉搏微弱，有 'Y' 和 'N' 两种；
- `Lung rales`：是否存在肺声，有 'Y' 和 'N' 两种；
- `Systolic Murmur`：是否存在收缩期杂音，有 'Y' 和 'N' 两种；
- `Diastolic Murmur`：是否存在扩张期杂音，有 'Y' 和 'N' 两种；
- `Dyspnea`：呼吸是否存在可能，有 'Y' 和 'N' 两种；
- `Atypical`：是否典型，有 'Y' 和 'N' 两种；
- `Nonanginal`：不存在心绞痛？有 'Y' 和 'N' 两种；
- `Exertional CP`：有 'Y' 和 'N' 两种；
- `LowTH Ang`：有 'Y' 和 'N' 两种；
- `LHP`：是否左心房肥大，有 'Y' 和 'N' 两种；
- `Poor R Progression`：有 'Y' 和 'N' 两种；
- `BBB`：有 'LBBB', 'N', 'RBBB' 三种；
- `VHD`：有 'Moderate', 'N', 'Severe', 'mild' 四种；

标签 `Cath` 也是一个文本型数据，有 'Cad' 和 'Normal' 两种情况。可见，除了 `BBB` 和 `VHD` 外，其他的文本数据都只有两种不同的取值，可以将其编码为0、1。而 `VHD` 的 4 中情况是有大小次序的，其中 `N` \< `mild`  \< `Moderate` \< `Severe`，故可以编码为 0，1，2，3，而对 `BBB` ，由于3种类型没有次序，因此使用 one-hot 编码。

代码如下：
```python
import numpy as np
import pandas as pd
from sklearn import preprocessing
from sklearn import utils

data = pd.read_excel("./Z-AlizadehSaniDataset.xlsx")

trans_code = {
    'Fmale': 0,
    'Male': 1,
    'N': 0,
    'Y': 1,
    'Normal': 0,
    'Cad': 1,
    'mild': 1,
    'Moderate': 2,
    'Severe': 3,
    0:0,
    1:1,
    False:0,
    True:1
}
data['BBB_N'] = (data['BBB']=='N')
data['LBBB'] = (data['BBB']=='LBBB')
data['RBBB'] = (data['BBB']=='RBBB')
del data['BBB']

for i in range(len(data.columns)):
    if (data.iloc[:, i].dtype==np.dtype('object') or
       data.iloc[:, i].dtype==np.dtype('bool')):
        data.iloc[:, i] = data.iloc[:, i].map(trans_code)

# 将 Cath 放在最后一行
data['tag'] = data['Cath']
del data['Cath']
data_X = data[data.columns[:-1]]
```

### 删除所有值相等的列

所有值都相等的列对判断明显是没有意义的，所以应该输出，代码如下：
```python
# 删除值永远不变的列
for i in data_X.columns:
    if all(data_X[i]==data_X[i][0]):
        del data_X[i]
```
其中 `Exertional CP` 的值始终为 0，故被删除。

### 数据标准化

数据标准化的公式为：$$\hat x_i=\frac{x_i-\bar x}{std(x_1,x2,\cdots,x_n)}$$ 其中 $std(x_1,x_2,\cdots,x_n)$ 表示样本标准差的无偏估计，$\bar x$ 表示样本均值，代码如下：

```python
# 对除了标签 Cath 外的特征进行标准化
data_X = (data_X-data_X.mean())/data_X.std()
data_X = data_X.values
```

现在查看数据集的样子为：
![](/homework/CalcAI/assets/clean_data.png)
总共为 `303 rows × 57 columns`。

### 使用 PCA 对数据进行降维

56 维的数据貌似不是很多，但为了提高训练效率，我们测试能否对数据进行降维，代码如下：

```python
import matplotlib
import matplotlib.pyplot as plt

X = np.mat(data.iloc[:, :-1])
cov_mat = X.T * X
eig_vals, eig_vects = np.linalg.eig(np.mat(cov_mat))
eig_val_indices = np.argsort(-eig_vals)
# 特征值的和
eig_sum = sum(eig_vals)
accu_sum = 0
plt_x = [0]
plt_y = [0]
for i in range(len(eig_vals)):
    plt_x.append(i+1)
    accu_sum += eig_vals[ eig_val_indices[i] ]
    plt_y.append(accu_sum/eig_sum*100)
print(accu_sum, eig_sum)
plt.figure(figsize=(10, 7))
plt.xlabel('eigenvalue cnt', fontsize=16)
plt.ylabel('percentage (%)', fontsize=16)
plt.scatter(plt_x, plt_y, color="deepskyblue")
plt.plot([0, len(eig_vals)], [90, 90], color='red')
plt.show()
```
下图显示的是随着使用特征值的增多，特征值占总特征值和的比例：
![](/homework/CalcAI/assets/eigenvalue_scatter.png)

图中的红线为 90% 的比例线，从图中可以看出，达到 90% 的比例，维度最多降低到40 左右，维度降低的比例不大，因此数据不使用 PCA 进行降维。

### 划分训练集和测试集

我们先将数据集打乱，并将其中的前250个数据，划分为测试，剩余的53个划分为训练集。

另外，为了使我们所编写的神经网络能够适应所有的分类问题，虽然只有 0-1 两种输出，我们仍然将输出变为 one-hot 编码，并将特征和标签分开，代码如下：
```python
cath_col = data['Cath'].values.reshape(-1,1)
cath_col = data['tag'].values.reshape(-1,1)

# del data['Cath']

# 对除了标签 Cath 外的特征进行标准化
data_y = preprocessing.OneHotEncoder().fit_transform(cath_col).toarray()

data_X, data_y = utils.shuffle(data_X, data_y)
train_cnt = 250
train_X = data_X[:train_cnt, :]
train_y = data_y[:train_cnt, :]
test_X = data_X[train_cnt:, :]
test_y = data_y[train_cnt:, :]
pd.DataFrame(np.hstack((train_X, train_y))).head()
```

## 神经网络训练

<h3 id="ANN"> 编写神经网络</h3>

神经网络类的代码如下：

```python
from sklearn import utils
import numpy as np
import pandas as pd
import matplotlib
import matplotlib.pyplot as plt

class Network(object):
    # 激活函数 sigmoid
    @classmethod
    def sigmoid(cls, z):
        return 1.0/(1.0+np.exp(-z))
    # 损失函数 MSE 的微分形式
    @classmethod
    def cost_derivative(cls, output_activations, y):
        return (output_activations-y)
    # 激活函数 sigmoid(x) 求导: sigmoid(x)*(1-sigmoid(x))
    @classmethod
    def sigmoid_prime(cls, z):
        sigmoid_z = cls.sigmoid(z)
        return sigmoid_z*(1-sigmoid_z)
    # 神经网络结构的初始化
    # sizes：序列，每个值代表一层神经网络节点个数，第1层为输入层，最后1层为输出层
    # activate：激活函数，默认使用 sigmoid函数
    # activate_prime：激活函数对应的微分
    # cost_derivative：损失函数导数，默认使用 MSE 损失函数的导数
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
    # 前向传播，获取输出
    def foreward(self, a, convert_f=None):
        for w,b in zip(self.weights, self.biases):
            a = self.activate_fun(np.dot(w, a) + b)
        return convert_f(a) if convert_f else a
    
    # epoches：训练的轮数
    # mini_batch_size：SGD 批量的大小
    # eta：学习率
    # 返回：一个序列，每个值是一次训练之后测试的准确率
    def train(self, train_x, train_y, epochs, mini_batch_size, eta,
            valid_x=None, valid_y=None):
        if type(valid_x)!=type(None) : n_valid = len(valid_x)
        x_columns_cnt = train_x.shape[1]
        train_data = np.hstack((train_x, train_y))
        n_train = len(train_data)
        accu_train = []
        accu_valid = []
        for j in range(epochs):
            np.random.shuffle(train_data)
            mini_batches = [train_data[i:i+mini_batch_size] 
                            for i in range(0,len(train_data),mini_batch_size)]
            for mini_batch in mini_batches:
                self.update_mini_batch(mini_batch[:, :x_columns_cnt],
                                       mini_batch[:, x_columns_cnt:], eta)
            accu_train.append(self.evaluate(train_x, train_y)/n_train)
            if type(valid_x)!=type(None):
                accu_valid.append(self.evaluate(valid_x, valid_y)/n_valid)
        return accu_train, accu_valid

    def update_mini_batch(self, xs, ys, eta):
        nabla_b = [np.zeros(b.shape) for b in self.biases]
        nabla_w = [np.zeros(w.shape) for w in self.weights]
        for x,y in zip(xs, ys):
            delta_nabla_b, delta_nabla_w = self.backprop(x, y)
            nabla_b = [nb+dnb for nb, dnb in zip(nabla_b, delta_nabla_b)]
            nabla_w = [nw+dnw for nw, dnw in zip(nabla_w, delta_nabla_w)]
        self.weights = [w - (eta/len(xs))*nw
                         for w, nw in zip(self.weights, nabla_w)]
        self.biases = [b + (eta/len(xs))*nb
                       for b, nb in zip(self.biases, nabla_b)]
        
    # 测试准确率
    def evaluate(self, test_x, test_y):
        test_result = 0
        for x, y in zip(test_x, test_y):
            x.shape = (-1, 1)
            y.shape = (-1, 1)
            test_result += int(np.argmax(y)==np.argmax(self.foreward(x)))
        return test_result
        
    def backprop(self, x, y):
        x.shape = (-1, 1)
        y.shape = (-1, 1)
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
我们需要确定的超参数主要是神经网络结构，包括神经网络的层数、每一层的节点个数。

我们使用 k-折交叉验证的方式 计算超参数对应的泛化准确率，K-折交叉验证函数的代码如下：
```python
# net_struct 神经网络结构
# data_x, data_y 训练集数据
# eta 学习率
# epoches 训练次数
# n_fold 折数
# 返回每次训练后 K 折的训练集上的准确率和验证集上的准确率
def kfold(net_struct, data_x, data_y, eta, epoches ,n_fold=10):
    train_accuracies = []
    valid_accuracies = []
    valid_cnt = int(len(data_x)/n_fold)
    np.random.seed(0)
    data_x, data_y = utils.shuffle(data_x, data_y)
    for i in range(0,len(data_x), valid_cnt):
        ann = Network(net_struct)
        t_x = np.vstack((data_x[:i,:], data_x[(i+valid_cnt):,:]))
        t_y = np.vstack((data_y[:i,:], data_y[(i+valid_cnt):,:]))
        v_x = data_x[i:(i+valid_cnt),:]
        v_y = data_y[i:(i+valid_cnt),:]
        ts, vs = ann.train(t_x, t_y, epoches, 10, eta, v_x, v_y)
        train_accuracies.append(ts)
        valid_accuracies.append(vs)
    train_a = np.array(train_accuracies)
    valid_a = np.array(valid_accuracies)
    return train_a.mean(axis=0), valid_a.mean(axis=0)
```

### 超参数的选择


训练次数越多，准确率通常越高，不过消耗的时间也越多，下面我们通过实验选择超参数。

我们选择学习率为 0.5，神经网络结构为 56×50×1，使用交叉验证来确定不同轮数在训练集和验证集上的准确率，代码如下：

```python
accu_train, accu_valid = kfold([56, 50, 1], train_X, train_y, 0.5, 100)

plot_x = np.arange(1, 101)  
plt.figure(figsize=(8, 5))
plt.plot(plot_x, accu_train, color="red", label="train_accuracy")
plt.plot(plot_x, accu_valid, color="green", label="valid_accuracy")
plt.legend(loc="best", fontsize=15)
plt.show()
```
绘制的图形为：
![](/homework/CalcAI/assets/56-50-2.png)

可以看出随着对数据集训练次数的增多，在训练集上的准确率和在验证集上的准确率都不断提高，并且在训练集上的准确率通常要高于在验证集上的准确率，这也符合我们的常识，并且在训练 40 次左右，验证集上的准确率接近 82%，训练 100 次左右，验证集上的准确率接近 85%，可见在40次左右，训练结果就较为稳定了。

在测试一下使用 56×20×1 的数据网络结构和 0.5 学习率进行训练的结果，代码为：
```python
accu_train, accu_valid = kfold([56, 20, 1], train_X, train_y, 0.5, 100)
plot_x = np.arange(1, 101)
    
plt.figure(figsize=(8, 5))
plt.plot(plot_x, accu_train, color="red", label="train_accuracy")
plt.plot(plot_x, accu_valid, color="green", label="valid_accuracy")
plt.legend(loc="best", fontsize=15)
plt.show()
```
测试结果为：
![](/homework/CalcAI/assets/56-20-2.png)

可以看出，神经网络的结构越简单，训练速度就越快，最终的结果为82%左右，可见，比 56×50×2 要差一点，不过效果差不多。

我们用其他模型进行训练，结果如下：
![](/homework/CalcAI/assets/result.png)

从上到下，从左到右，神经网络的结构越来越复杂，不过除了只有两层的神经网络结构后，其他的准确率都在 80% 左右，我们选择准确率为 85% 的 56×50×2 结构进行训练。

### 使用选定的超参数进行训练

将所有的 250 个实例都用于神经网络的训练，并用剩余的 53 个测试中的数据进行泛化性能的测试：
```python
# def train(self, train_x, train_y, epochs, mini_batch_size, eta
ann = Network([56, 50, 2])
accu_train, accu_test =  ann.train(train_X, train_y, 100, 10, 0.5, test_X, test_y)
plt.figure(figsize=(8, 5))
plt.plot(plot_x, accu_train, color="red", label="train_accuracy")
plt.plot(plot_x, accu_test, color="green", label="valid_accuracy")
plt.legend(loc="best", fontsize=15)
plt.title(f"train : {accu_train[-1]:.3f}, " +
         f"test : {accu_test[-1]:.3f}", fontsize=15)
plt.show()
```
结果为：
![](/homework/CalcAI/assets/test_figure.png)

最终的泛化准确率为 80% 左右 ，结果比较低。

## 使用sklearn中的库进行训练

### 数据预处理与划分

数据预处理和划分的过程和之前的过程非常类似，只是标签不需要使用 one-hot 进行编码。

### 构建与上述的神经网络

我们构建一个和手写的神经网络结构系统的网络，并且设置系统的学习率、激活函数、优化策略、批大小等超参数，代码如下：

```python
from sklearn import neural_network
clf = neural_network.MLPClassifier(hidden_layer_sizes=(50),
        activation='logistic',solver='sgd',alpha=0.5,
        batch_size=10,learning_rate='constant',max_iter=1000)
```

### 训练与测试神经网络

使用 `fit` 函数进行训练，使用 `predict` 函数进行测试：
```python
clf.fit(train_X, train_y)
sum(clf.predict(test_X)==test_y)/len(test_y)
```
测试结果为：`0.83`，和我们手写的函数准确率非常接近。

### 测试多组

```python
from sklearn import neural_network
nets = [(10,),(20,),(30,),(40,),(50,),(60,),(10,20),(20,10),(20,20),
       (30,10),(20,30),(10,10,10),(20,10,20),(20,20,20),(15,15,15,15)]
for net_struct in nets:
    clf = neural_network.MLPClassifier(hidden_layer_sizes=net_struct,
                                       activation='logistic',
                                       solver='sgd',
                                       alpha=0.5,
                                       batch_size=10,
                                       learning_rate='constant',
                                       max_iter=1000)
    clf.fit(train_X, train_y)
    print(f'{net_struct} : {sum(clf.predict(test_X)==test_y)/len(test_y)}')
```

测试结果为：
```
(10,) : 0.8490566037735849
(20,) : 0.8490566037735849
(30,) : 0.8490566037735849
(40,) : 0.8490566037735849
(50,) : 0.8490566037735849
(60,) : 0.8490566037735849
(10, 20) : 0.7735849056603774
(20, 10) : 0.7735849056603774
(20, 20) : 0.7735849056603774
(30, 10) : 0.7735849056603774
(20, 30) : 0.7735849056603774
(10, 10, 10) : 0.7735849056603774
(20, 10, 20) : 0.7735849056603774
(20, 20, 20) : 0.7735849056603774
(15, 15, 15, 15) : 0.7735849056603774
```

### 使用默认参数进行测试

不使用我们指定的参数，而直接使用 `MLPClassifier` 自带的默认参数：

```python
from sklearn import neural_network
nets = [(10,),(20,),(30,),(40,),(50,),(60,),(10,20),(20,10),(20,20),
       (30,10),(20,30),(10,10,10),(20,10,20),(20,20,20),(15,15,15,15)]
for net_struct in nets:
    clf = neural_network.MLPClassifier()
    clf.fit(train_X, train_y)
    print(f'{net_struct} : {sum(clf.predict(test_X)==test_y)/len(test_y)}')
```
结果为：
```
(10,) : 0.9056603773584906
(20,) : 0.9056603773584906
(30,) : 0.9056603773584906
(40,) : 0.9056603773584906
(50,) : 0.8867924528301887
(60,) : 0.8867924528301887
(10, 20) : 0.9245283018867925
(20, 10) : 0.9056603773584906
(20, 20) : 0.9056603773584906
(30, 10) : 0.9245283018867925
(20, 30) : 0.9245283018867925
(10, 10, 10) : 0.8867924528301887
(20, 10, 20) : 0.9056603773584906
(20, 20, 20) : 0.8867924528301887
(15, 15, 15, 15) : 0.8867924528301887
```
可以看出，要比我们指定的参数好一些。
