# 常用层

常用层对应于core模块，core内部定义了一系列常用的网络层，包括全连接、激活层等

## Dense层
```python
keras.layers.core.Dense(output_dim, init='glorot_uniform', activation='linear', weights=None, W_regularizer=None, b_regularizer=None, activity_regularizer=None, W_constraint=None, b_constraint=None, bias=True, input_dim=None)
```
Dense就是常用的全连接层，这里是一个使用示例：

```python
# as first layer in a sequential model:
model = Sequential()
model.add(Dense(32, input_dim=16))
# now the model will take as input arrays of shape (*, 16)
# and output arrays of shape (*, 32)

# this is equivalent to the above:
model = Sequential()
model.add(Dense(32, input_shape=(16,)))

# after the first layer, you don't need to specify
# the size of the input anymore:
model.add(Dense(32))
```

### 参数：

* output_dim：大于0的整数，代表该层的输出维度。模型中非首层的全连接层其输入维度可以自动推断，因此非首层的全连接定义时不需要指定输入维度。

* init：初始化方法，为预定义初始化方法名的字符串，或用于初始化权重的Theano函数。该参数仅在不传递```weights```参数时才有意义。

* activation：激活函数，为预定义的激活函数名（参考[<font color='#FF0000'>激活函数</font>](../other/activations)），或逐元素（element-wise）的Theano函数。如果不指定该参数，将不会使用任何激活函数（即使用线性激活函数：a(x)=x）

* weights：权值，为numpy array的list。该list应含有一个形如（input_dim,output_dim）的权重矩阵和一个形如(output_dim,)的偏置向量。

* W_regularizer：施加在权重上的正则项，为[<font color='FF0000'>WeightRegularizer</font>](../other/regularizers)对象

* b_regularizer：施加在偏置向量上的正则项，为[<font color='FF0000'>WeightRegularizer</font>](../other/regularizers)对象

* activity_regularizer：施加在输出上的正则项，为[<font color='FF0000'>ActivityRegularizer</font>](../other/regularizers)对象

* W_constraints：施加在权重上的约束项，为[<font color='FF0000'>Constraints</font>](../other/constraints)对象

* b_constraints：施加在偏置上的约束项，为[<font color='FF0000'>Constraints</font>](../other/constraints)对象

* bias：布尔值，是否包含偏置向量（即层对输入做线性变换还是仿射变换）

* input_dim：整数，输入数据的维度。当Dense层作为网络的第一层时，必须指定该参数或```input_shape```参数。

### 输入

形如(nb_samples, ..., input_dim)的nD张量，最常见的情况为(nb_samples, input_dim)的2D张量

### 输出

形如(nb_samples, ..., output_dim)的nD张量，最常见的情况为(nb_samples, output_dim)的2D张量

***

<a name='activation'>
<font color='#404040'>
## Activation层
</font></a>
```python
keras.layers.core.Activation(activation)
```
激活层对一个层的输出施加激活函数

### 参数

* activation：将要使用的激活函数，为预定义激活函数名或一个Tensorflow/Theano的函数。参考[<font color='#FF0000'>激活函数</font>](../other/activations)

### 输入shape

任意，当使用激活层作为第一层时，要指定```input_shape```

### 输出shape

与输入shape相同

***
</a name='dropout'>
<font color='#404040'>
## Dropout层
</font></a>
```python
keras.layers.core.Dropout(p)
```
为输入数据施加Dropout。Dropout将在训练过程中每次更新参数时随机断开一定百分比（p）的输入神经元连接，Dropout层用于防止过拟合。

### 参数

* p：0~1的浮点数，控制需要断开的链接的比例

### 参考文献

* [<font color='FF0000'>Dropout: A Simple Way to Prevent Neural Networks from Overfitting</font>](http://www.cs.toronto.edu/~rsalakhu/papers/srivastava14a.pdf)

***


## Flatten层
```python
keras.layers.core.Flatten()
```
Flatten层用来将输入“压平”，即把多维的输入一维化，常用在从卷积层到全连接层的过渡。Flatten不影响batch的大小。

### 例子
```python
model = Sequential()
model.add(Convolution2D(64, 3, 3, border_mode='same', input_shape=(3, 32, 32)))
# now: model.output_shape == (None, 64, 32, 32)

model.add(Flatten())
# now: model.output_shape == (None, 65536)
```

***

## Reshape层
```python
keras.layers.core.Reshape(target_shape)
```
Reshape层用来将输入shape转换为特定的shape

### 参数

* target_shape：目标shape，为整数的tuple，不包含样本数目的维度（batch大小）

### 输入shape

任意，但输入的shape必须固定。当使用该层为模型首层时，需要指定```input_shape```参数

### 输出shape

```(batch_size,)+target_shape```

### 例子

```python
# as first layer in a Sequential model
model = Sequential()
model.add(Reshape((3, 4), input_shape=(12,)))
# now: model.output_shape == (None, 3, 4)
# note: `None` is the batch dimension

# as intermediate layer in a Sequential model
model.add(Reshape((6, 2)))
# now: model.output_shape == (None, 6, 2)
```

***

## Permute层
```python
keras.layers.core.Permute(dims)
```
Permute层将输入的维度按照给定模式进行重排，例如，当需要将RNN和CNN网络连接时，可能会用到该层。

### 参数

* dims：整数tuple，指定重排的模式，不包含样本数的维度。重拍模式的下标从1开始。例如（2，1）代表将输入的第二个维度重拍到输出的第一个维度，而将输入的第一个维度重排到第二个维度

### 例子

```python
model = Sequential()
model.add(Permute((2, 1), input_shape=(10, 64)))
# now: model.output_shape == (None, 64, 10)
# note: `None` is the batch dimension
```

### 输入shape

任意，当使用激活层作为第一层时，要指定```input_shape```

### 输出shape

与输入相同，但是其维度按照指定的模式重新排列

***

## RepeatVector层
```python
keras.layers.core.RepeatVector(n)
```
RepeatVector层将输入重复n次

### 参数

* n：整数，重复的次数

### 输入shape

形如（nb_samples, features）的2D张量

### 输出shape

形如（nb_samples, n, features）的3D张量

### 例子
```python
model = Sequential()
model.add(Dense(32, input_dim=32))
# now: model.output_shape == (None, 32)
# note: `None` is the batch dimension

model.add(RepeatVector(3))
# now: model.output_shape == (None, 3, 32)
```

***

## Merge层
```python
keras.engine.topology.Merge(layers=None, mode='sum', concat_axis=-1, dot_axes=-1, output_shape=None, node_indices=None, tensor_indices=None, name=None)
```
Merge层根据给定的模式，将一个张量列表中的若干张量合并为一个单独的张量

### 参数

* layers：该参数为Keras张量的列表，或Keras层对象的列表。该列表的元素数目必须大于1。
* mode：合并模式，为预定义合并模式名的字符串或lambda函数或普通函数，如果为lambda函数或普通函数，则该函数必须接受一个张量的list作为输入，并返回一个张量。如果为字符串，则必须是下列值之一：
	* “sum”，“mul”，“concat”，“ave”，“cos”，“dot”

* concat_axis：整数，当```mode=concat```时指定需要串联的轴

* dot_axes：整数或整数tuple，当```mode=dot```时，指定要消去的轴

* output_shape：整数tuple或lambda函数/普通函数（当mode为函数时）。如果output_shape是函数时，该函数的输入值应为一一对应于输入shape的list，并返回输出张量的shape。

* node_indices：可选，为整数list，如果有些层具有多个输出节点（node）的话，该参数可以指定需要merge的那些节点的下标。如果没有提供，该参数的默认值为全0向量，即合并输入层0号节点的输出值。

* tensor_indices：可选，为整数list，如果有些层返回多个输出张量的话，该参数用以指定需要合并的那些张量。

### 例子
```python
model1 = Sequential()
model1.add(Dense(32))

model2 = Sequential()
model2.add(Dense(32))

merged_model = Sequential()
merged_model.add(Merge([model1, model2], mode='concat', concat_axis=1)
- ____TODO__: would this actually work? it needs to.__

# achieve this with get_source_inputs in Sequential.
```

***

## Lambda层
```python
keras.layers.core.Lambda(function, output_shape=None, arguments={})
```
本函数用以对上一层的输出施以任何Theano/TensorFlow表达式

### 参数

* function：要实现的函数，该函数仅接受一个变量，即上一层的输出

* output_shape：函数应该返回的值的shape，可以是一个tuple，也可以是一个根据输入shape计算输出shape的函数

* arguments：可选，字典，用来记录向函数中传递的其他关键字参数

### 例子
```python
# add a x -> x^2 layer
model.add(Lambda(lambda x: x ** 2))
```

```python
# add a layer that returns the concatenation
# of the positive part of the input and
# the opposite of the negative part

def antirectifier(x):
    x -= K.mean(x, axis=1, keepdims=True)
    x = K.l2_normalize(x, axis=1)
    pos = K.relu(x)
    neg = K.relu(-x)
    return K.concatenate([pos, neg], axis=1)

def antirectifier_output_shape(input_shape):
    shape = list(input_shape)
    assert len(shape) == 2  # only valid for 2D tensors
    shape[-1] *= 2
    return tuple(shape)

model.add(Lambda(antirectifier, output_shape=antirectifier_output_shape))
```
### 输入shape

任意，当使用该层作为第一层时，要指定```input_shape```

### 输出shape

由```output_shape```参数指定的输出shape

***

## ActivityRegularizer层
```python
keras.layers.core.ActivityRegularization(l1=0.0, l2=0.0)
```

经过本层的数据不会有任何变化，但会基于其激活值更新损失函数值

### 参数

* l1：1范数正则因子（正浮点数）

* l2：2范数正则因子（正浮点数）

### 输入shape

任意，当使用该层作为第一层时，要指定```input_shape```

### 输出shape

与输入shape相同

***

## Masking层
```python
keras.layers.core.Masking(mask_value=0.0)
```

使用给定的值对输入的序列信号进行“屏蔽”，用以定位需要跳过的时间步

对于输入张量的时间步，即输入张量的第1维度（维度从0开始算，见例子），如果输入张量在该时间步上都等于```mask_value```，则该时间步将在模型接下来的所有层（只要支持masking）被跳过（屏蔽）。

如果模型接下来的一些层不支持masking，却接受到masking过的数据，则抛出异常。

### 例子

考虑输入数据```x```是一个形如(samples,timesteps,features)的张量，现将其送入LSTM层。因为你缺少时间步为3和5的信号，所以你希望将其掩盖。这时候应该：

* 赋值```x[:,3,:] = 0.```，```x[:,5,:] = 0.```

* 在LSTM层之前插入```mask_value=0.```的```Masking```层

```python
model = Sequential()
model.add(Masking(mask_value=0., input_shape=(timesteps, features)))
model.add(LSTM(32))
```

***

## Highway层
```python
keras.layers.core.Highway(init='glorot_uniform', transform_bias=-2, activation='linear', weights=None, W_regularizer=None, b_regularizer=None, activity_regularizer=None, W_constraint=None, b_constraint=None, bias=True, input_dim=None)
```

Highway层建立全连接的Highway网络，这是LSTM在前馈神经网络中的推广

### 参数：

* output_dim：大于0的整数，代表该层的输出维度。模型中非首层的全连接层其输入维度可以自动推断，因此非首层的全连接定义时不需要指定输入维度。

* init：初始化方法，为预定义初始化方法名的字符串，或用于初始化权重的Theano函数。该参数仅在不传递```weights```参数时有意义。

* activation：激活函数，为预定义的激活函数名（参考[<font color='#FF0000'>激活函数</font>](../other/activations)），或逐元素（element-wise）的Theano函数。如果不指定该参数，将不会使用任何激活函数（即使用线性激活函数：a(x)=x）

* weights：权值，为numpy array的list。该list应含有一个形如（input_dim,output_dim）的权重矩阵和一个形如(output_dim,)的偏置向量。

* W_regularizer：施加在权重上的正则项，为[<font color='FF0000'>WeightRegularizer</font>](../other/regularizers)对象

* b_regularizer：施加在偏置向量上的正则项，为[<font color='FF0000'>WeightRegularizer</font>](../other/regularizers)对象

* activity_regularizer：施加在输出上的正则项，为[<font color='FF0000'>ActivityRegularizer</font>](../other/regularizers)对象

* W_constraints：施加在权重上的约束项，为[<font color='FF0000'>Constraints</font>](../other/constraints)对象

* b_constraints：施加在偏置上的约束项，为[<font color='FF0000'>Constraints</font>](../other/constraints)对象

* bias：布尔值，是否包含偏置向量（即层对输入做线性变换还是仿射变换）

* input_dim：整数，输入数据的维度。当该层作为网络的第一层时，必须指定该参数或```input_shape```参数。

* transform_bias：用以初始化传递参数，默认为-2（请参考文献理解本参数的含义）

### 输入shape

形如（nb_samples, input_dim）的2D张量

### 输出shape

形如（nb_samples, output_dim）的2D张量

### 参考文献

* [<font color='FF0000'>Highway Networks</font>](http://arxiv.org/pdf/1505.00387v2.pdf)

***

## MaxoutDense层

全连接的Maxout层

```MaxoutDense```层以```nb_features```个```Dense(input_dim,output_dim)```线性层的输出的最大值为输出。```MaxoutDense```可对输入学习出一个凸的、分段线性的激活函数。

### 参数

* nb_features：内部使用的全连接层的数目

### 输入shape

形如（nb_samples, input_dim）的2D张量

### 输出shape

形如（nb_samples, output_dim）的2D张量

### 参考文献

* [<font color='FF0000'>Maxout Networks</font>](http://arxiv.org/pdf/1302.4389.pdf)
































