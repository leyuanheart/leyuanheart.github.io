---
layout:     post
title:      Deep learning from scratch 1
subtitle:   Perceptron and Neural Network
date:       2021-02-13
author:     Le Yuan
header-img: img/wolf.jpg
catalog: true
tags:
    - Deep Learning
---

<img src="https://img.imgdb.cn/item/60311e2d5f4313ce25b859d6.jpg" alt="fengmian" style="zoom:50%;" />

这一系列开始记录这本书的学习过程。其实我是2019年就买了这本书读完了，如今算是重新捡起来再看，因为第一遍看的时候就觉得这本书对于深度学习的介绍非常深入浅出，没有像很多深度学习相关的书一样，给你介绍一些深度学习的框架，TensorFlow、Pytorch都没有，也不会介绍如何使用GPU加速啊，抛开这些“高大上”的东西（当然，如果要从事深度学习相关的工作，这些很重要...），就用python里最常用的和Matplotlib来做代码的演示，从最基础的概念讲起，书长得也是很小巧，总共300页不到，我记得我当时几乎就是花了2天一口气读完了，感觉好顺畅（可能确实也是因为很基础，较为简单，但是依然是有收获的）。所以这次重看，我就把一些我觉得重要的一些内容进行一个归纳，即是作为整理，方便自己查阅，也希望能有一些新的认识。

哦，对了，这本书现在应该已经出到第三版了，它书上的配套代码都可以在[Oreilly-Japan](https://github.com/oreilly-japan)这个github上找到，我扫了一眼后面两版，发现已经和我看过的这个第一版在内容上差别很大了，第二版是讲自然语言处理（第一版主要是以图像来举例的），然后第三版我也没看明白是讲了什么（因为是用日文写的介绍），但是第一版的内容我觉得并不会过时，我认为不管是什么阶段学习深度学习的人，看这本书都会有自己的收获。再申明一件事，博客里大部分的文字和图片会使用这本书中的内容，如果是我自己的一些想法，我会说明的（是不是感觉和一般的申明反过来了...，这是我自己的问题，我是这个年三十决定开始写的，但是之前在读的时候并没有做过笔记，现在手边只有这本书，而且我也不想花太多时间在这上面，毕竟还要写论文呢...，但是又不想让之前看过的这本书就这么过去了，所以就决定这么办，如果涉及到版权问题，我到时候就把文章撤了，在这里先道个歉）。

好了，进入正题，第一章是介绍一些python的使用，我就跳过了，从第二章**感知机**开始。

## 感知机 (Perceptron)

感知机是由美国学者Frank Rosenblatt在1957年提出来的，它是作为神经网络（深度学习）起源的算法。

感知机接收多个输入信号，输出一个信号。这里所说的“信号”可以想象成电流或者河流那样具备“流动性”的东西。但是，和实际的电流不同的是，感知机的信号只有“流/不流”（1/0）两种取值。

下图是一个感知机的例子。$x_1,x_2$是输入信号，$y$是输出信号，$w_1, w_2$是权重。图中的$\bigcirc$称为“神经元”或者“节点”。输入信号被送往神经元，会被分别乘以固定的权重（$w_1x_1,w_2x_2$）。神经元会计算传过来的信号的总和，只有当这个总和超过了某个界限值时，才会输出1，这也称为“神经元被激活”。这里将这个界限值称为**阈值**，用$\theta$表示。

<img src="https://img.imgdb.cn/item/60311e555f4313ce25b87069.png" alt="1" style="zoom:50%;" />

把上述内容用数学公式表达出来，就是：


$$
y = 
\begin{cases}
0, & w_1x_1+w_2x_2 \leq \theta \\
1, & w_1x_1+w_2x_2 > \theta
\end{cases}
$$


这些权重发挥着控制各个信号重要性的作用，权重越大，信号的重要性就越高。可以类比于电流里所说的电阻，电阻越低，通过的电流就越大。两者都是控制信号流动程度的。

### 简单逻辑电路

#### 与门 (AND gate)、与非门 (NAND gate)和或门 (OR gate)

我们考虑用感知机来表示逻辑电路。

<img src="https://img.imgdb.cn/item/60311e555f4313ce25b8706b.png" alt="2" style="zoom:150%;" />

以与门来举例说明，我们需要确定能满足图中输入输出结果的$w_1,w_2,\theta$。实际上，满足条件的参数的选组方法有无数多个。比如，$(w_1,w_2,\theta)=(0.5,0.5,0.7),(0.5,0.5,0.8),(1,1,1)$。

> 这里决定感知机参数的并不是计算机，而是我们人。我们看着真值表这种“训练数据”，人工考虑（想到）了参数的值。而机器学习的课题就是将这个决定参数值的工作交由计算机自动进行。**学习**是确定合适参数的过程，而人要做的是思考感知机的构造（模型），并把训练数据交给计算机。

这里重要的一点是：与门、与非门、或门的感知机构造是一样的。实际上，3个门电路只有参数的值不同。

#### 引入权重和偏置

为了以后的事情，我们对之前感知机的数学表达式进行一些修改，


$$
y = \begin{cases}0, & b+w_1x_1+w_2x_2 \leq 0 \\1, & b+w_1x_1+w_2x_2 > 0\end{cases}
$$


虽然符号不同，但是两者表达的内容是完全相同的。此处，$b$称为**偏置**，$w_1,w_2$称为**权重**。但是请注意，**偏置和权重的作用是不一样的**。具体地说，$w_1,w_2$是控制输入信号的重要性的参数，而偏置$b$是调整神经元被激活的容易程度的参数。比如，若$b$为$-0.1$，则只要输入信号的加权总和超过$0.1$，神经元就会被激活。但是，如果$b$为$-20$，则只要输入信号的加权总和必须超过$20$，神经元才会被激活。

#### 感知机的实现

```python
import numpy as np
def AND(x1, x2):
    x = np.array([x1, x2])
    w = np.array([0.5, 0.5])
    b = -0.7
    tmp = np.sum(w*x) + b
    if tmp <= 0:
        return 0
    else:
        return 1
  
def NAND(x1, x2):
    x = np.array([x1, x2])
    w = np.array([-0.5, -0.5])  # 仅权重与偏置不同！
    
    b = 0.7
    tmp = np.sum(w*x) + b
    if tmp <= 0:
        return 0
    else:
        return 1
    
def OR(x1, x2):
    x = np.array([x1, x2])
    w = np.array([0.5, 0.5])    # 仅权重与偏置不同！
    
    b = -0.2
    tmp = np.sum(w*x) + b
    if tmp <= 0:
        return 0
    else:
        return 1
```

### 感知机的局限

感知机无法实现异或门 (XOR gate)

<img src="https://img.imgdb.cn/item/60311e555f4313ce25b8706e.png" alt="3" style="zoom:80%;" />

我们用图形来说明。当权重参数$(b,w_1,w_2)=(-0.5,1.0,1.0)$时，感知机会生成一条由直线$-0.5+x_1+x_2=0$的直线分隔开的两个空间，其中一个空间输出1，另一个输出0。

<img src="https://img.imgdb.cn/item/60311e555f4313ce25b87073.png" alt="4" style="zoom:80%;" />

对于或门来说，是可以找到一条直线将$\bigcirc$和$\triangle$分开的，但是对于异或门来说，是无法找到一条直线将它们分开的。

#### 线性和非线性

如果将“直线”这个限制条件去掉，就有办法分开，比如像下图一样：

<img src="https://img.imgdb.cn/item/60311e555f4313ce25b87075.png" alt="5" style="zoom:80%;" />

（单层）感知机的局限性就在于它只能表示由一条直线分割的空间。

### 多层感知机 (Multi-layered perceptron)

感知机的绝妙之处在于它可以“叠加层”。

#### 已有门电路的组合

异或门的制作方法有很多，其中之一就是组合前面做好的与门、与非门和或门。按下图的方式组合之后，就能够表示异或门了。

<img src="https://img.imgdb.cn/item/60311e865f4313ce25b8890a.png" alt="6" style="zoom:80%;" />

通过下面的真值表也可以验证这样的配置确实是实现了异或门。

<img src="https://img.imgdb.cn/item/60311e865f4313ce25b8890c.png" alt="7" style="zoom:80%;" />

下面我们试着用感知机的表示方法（明确地显示神经元）来表示这个异或门。

<img src="https://img.imgdb.cn/item/60311e865f4313ce25b8890e.png" alt="8" style="zoom:80%;" />

异或门是一种多层次的神经网络。这里，将最左边的一列称为第0层，中间一列称为第一层，最右边一列称为第2层。因为拥有权重的层实际上只有2层（第0层和第1层之间，第1层和第2层之间），所以称为“2层感知机”。

#### 异或门的实现

```python
def XOR(x1, x2):
    s1 = NAND(x1, x2)
    s2 = OR(x1, x2)
    y = AND(s1, s2)
    return y
```

### 从与非门到计算机

这一节主要想表达的意思是：多层感知机能够实现非常复杂的功能，甚至可以表示计算机！因为**只需要通过与非门的结合，就能再现计算机进行的处理**。（想要深入了解这个问题的小伙伴，书中有建议去看一下《计算机系统要素：从零开始构建现代计算机》这本书。这里就当做一个奇怪的小知识记一记吧，哈哈哈。）

## 神经网络

关于感知机，既有好消息，也有坏消息。好消息是，即便对于复杂的函数，感知机也隐含着能够表示它的可能性。坏消息是，设定权重的工作，即确定合适的、能符合预期的输入与输出的权重，现在还是由人工进行的。**神经网络**的出现就是为了解决刚才的坏消息的。具体地讲，神经网络的一个重要性质是它可以自动地从数据中学习到合适的权重参数。

### 从感知机到神经网络

<img src="https://img.imgdb.cn/item/60311e865f4313ce25b88910.png" alt="9" style="zoom:80%;" />

用图来表示神经网络的话，其实和感知机非常类似。我们把最左边的一列称为**输入层**，最右边的一列称为**输出层**，中间的一列称为**中间层**。中间层有时也称为**隐藏层**。“隐藏”一词的意思是，隐藏层的神经元（和输入、输出层不同）肉眼看不见。

我们再回顾一下感知机的数学表达：


$$
y = \begin{cases}0, & b+w_1x_1+w_2x_2 \leq 0 \\1, & b+w_1x_1+w_2x_2 > 0\end{cases}
$$


在之前的图中，偏置$b$并没有被画出来，如果要明确地表示$b$，可以像下图一样，添加一个权重为$b$的输入信号1。另外，我们引入一个函数$h(x)$，进一步简化上面的表示式：


$$
\begin{align}
&a = b+w_1x_1+w_2x_2 \\
& y=h(a) \\
& h(x) = \begin{cases}
0, & x \leq 0 \\
1, & x > 0
\end{cases}
\end{align}
$$



<img src="https://img.imgdb.cn/item/60311e865f4313ce25b88914.png" alt="10" style="zoom:67%;" />



刚才登场的$h(x)$会将输入信号的总和转化为输出信号，这种函数一般称为**激活函数**（activation function）。如“激活”一词所示，激活函数的作用在于决定如何来激活输入信号的总和。

上图的$\bigcirc$中明确显示了激活函数的计算过程。即信号的加权总和为节点$a$，然后节点$a$被激活函数$h()$转化成节点$y$。

### 激活函数

激活函数是连接感知机和神经网络的桥梁。

感知机中使用的激活函数被称为“阶跃函数”（step function），它以阈值为界，一旦输入超过阈值，就切换输出。实际上，如果将激活函数从阶跃韩式换成其他函数，就可以进入神经网络的世界了。

#### sigmoid函数

神经网络中经常使用的一个激活函数就是**sigmoid**的函数：


$$
h(x)=\frac{1}{1+e^{-x}}
$$


现在来比较一下sigmoid函数和阶跃函数，下图是两者的图像，虚线是阶跃函数，实线是sigmoid函数。首先注意到的是“平滑性”的不同。sigmoid韩式是一条平滑的曲线，输出随着输入发生连续型的变化；而阶跃函数以0为界，输出发生急剧的变化，且只能返回0或1。也就是说，感知机中神经元之间流动的是0或1的二元信号，而神经网络中流动的是连续的实数值信号。

<img src="https://img.imgdb.cn/item/60311ec65f4313ce25b8a957.png" alt="11" style="zoom:80%;" />

再来说说两者的共同点，从宏观的视角看，它们具有相似的形状，两者的结构均为“输入小时，输出接近0（为0），随着输入增大，输出向1靠近（变成1）”。另一个共同点是：两者均为**非线性函数**。神经网络的激活函数必须使用非线性函数。如果使用线性函数，那么叠加层就没有意义了，多个线性组合的叠加仍然是一个线性组合，所以为了发挥叠加层所带来的优势，激活函数必须使用非线性函数。

> 这里在使用“感知机”一词时，没有严格统一它所指的算法。一般而言，“朴素感知机”是指单层网络，指的是激活函数是“阶跃函数”的模型。“多层感知机”是指神经网络，即使用sigmoid函数等平滑函数的多层网络。

#### ReLU函数

另一种使用非常广泛的激活函数是**ReLU**（Rectified Linear Unit）函数：


$$
h(x)=\begin{cases}
x, & x>0 \\
0, & x\leq 0
\end{cases}
$$


<img src="https://img.imgdb.cn/item/60311ec65f4313ce25b8a959.png" alt="12" style="zoom:80%;" />

#### 3层神经网络的实现

我们先从定义符号开始，如下图所示，权重和隐藏层的神经元的右上角有一个“（1）”，它表示权重和神经元的层号（即第1层的权重、第1层的神经元）。此外，权重的右下角有两个数字，它们是后一层的神经元和前一层的神经元的索引号。比如，$w_{12}^{(1)}$表示前一层的第2个神经元$x_2$到后一层的第1个神经元$a_1^{(1)}$的权重。

<img src="https://img.imgdb.cn/item/60311ec65f4313ce25b8a95c.png" alt="13" style="zoom:80%;" />

现在看一下从输入层到第1层的第1个神经元的信号传递过程，如下图：

<img src="https://img.imgdb.cn/item/60311ec65f4313ce25b8a960.png" alt="14" style="zoom:80%;" />

图中增加了表示偏置的神经元“1”。请注意，偏置的右下角的索引号只有一个，对应的是后一层神经元的位置。隐藏层的加权和用$a_1^{(1)}$表示，被激活函数转换后的信号用$z$表示，这里用第1个神经元来举例：


$$
\begin{align}
&a_1^{(1)}=w_{11}^{(1)}x_1+w_{12}^{(1)}x_2+b_1^{(1)} \\
&z_1^{(1)} = h(a_1^{(1)})
\end{align}
$$


此外，如果使用矩阵乘法运算，则可以将第一层的操作表示成下面的格式：


$$
\begin{align}
&A^{(1)} = XW^{(1)}+B^{(1)} \\
&Z^{(1)} = h(A^{(1)})
\end{align}
$$


其中，$A^{(1)}=(a_1^{(1)}, a_2^{(1)},a_3^{(1)})$，$X=(x_1, x_2)$，$B^{(1)}=(b_1^{(1)},b_2^{(1)},b_3^{(1)})$，$Z^{(1)}=(z_1^{(1)}, z_2^{(1)},z_3^{(1)})$，$W^{(1)}=\begin{pmatrix} w_{11}^{(1)}&w_{21}^{(1)}&w_{31}^{(1)}\\\\w_{12}^{(1)}&w_{22}^{(1)}&w_{32}^{(1)}\end{pmatrix}$。



第1层到第2层的信号传递也是类似的：

<img src="https://img.imgdb.cn/item/60311ec65f4313ce25b8a963.png" alt="15" style="zoom:80%;" />

最后是第2层到输出层的信号传递。也基本类似，不过最后的激活函数和之前的隐藏层有所不同。这里我们定义了identify_function()函数（也称为“恒等函数”），并将其作为输出层的激活函数。恒等函数会将输入按照原样输出，因此，在这个例子里其实没有必要特意定义这个函数，这里这样实现只是为了和之前的流程保持统一。另外输出层的激活函数用$\sigma()$表示，不同于隐藏层的激活函数$h()$。

<img src="https://img.imgdb.cn/item/60311eff5f4313ce25b8c55b.png" alt="16" style="zoom:80%;" />

代码总结如下：

```python
import numpy as np

def sigmoid(x):
    return 1./(1 + np.exp(-x))

def identify_function(x):
    return x

def init_network():
    network = {}
    network['W1'] = np.array([[0.1, 0.3, 0.5], [0.2, 0.4, 0.6]])
    network['b1'] = np.array([0.1, 0.2, 0.3])
    network['W2'] = np.array([[0.1, 0.4], [0.2, 0.5], [0.3, 0.6]])
    network['b2'] = np.array([0.1, 0.2])
    network['W3'] = np.array([[0.1, 0.3], [0.2, 0.4]])
    network['b3'] = np.array([0.1, 0.2])
    
    return network

def forward(network, x):
    W1, W2, W3 = network['W1'], network['W2'], network['W3']
    b1, b2, b3 = network['b1'], network['b2'], network['b3']
    
    a1 = np.dot(x, W1) + b1
    z1 = sigmoid(a1)
    a2 = np.dot(z1, W2) + b2
    z2 = sigmoid(a2)
    a3 = np.dot(z2, W3) + b3
    y = identify_funtion(a3)
    
    return y


network = init_network()
x = np.array([1.0, 0.5])
y = forward(network, x)
print(y)
```

这里定义了init_network()和forward()函数。init_network()会进行权重和偏置的初始化，并将它们保存在字典变量network中。forward()函数则封装了将输入信号转化为输出信号的处理过程。

另外这里出现了forward（前向）一词，它表示的是才从输入到输出方向的传递处理。后面在进行神经网络训练时，将介绍后向（backward，从输出到输入方向）的处理。

#### 输出层的设计

输出层所用的激活函数，要根据求解问题的性质决定。一般地，回归问题可以用恒等函数，二元分类问题可以使用sigmoid函数，多元分类问题可以使用softmax函数。

在输出层使用恒等函数时，如果用图来表示的，就是下图左边的情况，很等函数进行的转换处理可以用一根箭头来表示。分类问题可以使用softmax函数：


$$
y_k=\frac{\exp(a_k)}{\sum_{i=1}^n\exp(a_i)}
$$


如果用图来表示的话，就是下图的右边，softmax的输出通过箭头与所有的输入信号相连。

<img src="https://img.imgdb.cn/item/60311eff5f4313ce25b8c55d.png" alt="17" style="zoom:67%;" />

在实现softmax函数时有一个需要注意的事项，就是**数值溢出**。softmax函数实现中要进行指数的运算，但是此时指数的值很容易变得非常大。比如，$e^{10}$的值就会超过20000，$e^{100}$会变成一个后面有40多个0的超大值，$e^{1000}$结果会返回一个表示无穷大的$\inf$。如果这些超大值之间进行除法的话，就会出现“不确定（nan）”的情况。

所以需要设计一下，我们利用softmax函数的一个性质：

![18](https://img.imgdb.cn/item/60311eff5f4313ce25b8c55f.png)

即在进行指数运算时，加上（或者减去）某个常数并不会改变运算的结果。于是我们可以通过减去输入信号中的最大值，来解决溢出的问题。

```python
import numpy as np
def softmax(a):
    c = np.max(a)
    exp_a = np.exp(a-c)
    sum_exp_a = np.sum(exp_a)
    y = exp_a / sum_exp_a
    
    return y
```

softmax函数的输出是0到1之间的实数。并且，输出值的总和为1。正是因为有了这个性质，我们才可以把softmax函数的输出解释为“概率”。而且，softmax函数不会改变各元素之间的大小关系。由于指数运算需要一定的计算机运算量，因此在推理阶段，输出层的softmax函数一般会被省略。

> 求解机器学习问题的步骤可以分为“学习”（Learning）和“推理”（Inference）两个阶段。在学习阶段济宁模型的学习（也叫训练），然后用学到的模型对未知的数据进行推理（分类问题就是分类）。如前所述，推理阶段一般会省略输出层的softmax函数。在输出层使用softmax函数是因为它和神经网络的学习有关系（这个之后会介绍）。

#### 批处理

最后介绍一下批处理，原书这里是用了手写数字MNIST数据集来做了一个实例分析，我就不详细介绍了，就里面这个batch的概念我觉得需要强调一下。

我们首先看一下一张图片在处理过程中的形状变化：

![19](https://img.imgdb.cn/item/60311eff5f4313ce25b8c562.png)

输入一个784个元素（原本是一个$28\times28$的二维数组，然后打平了）构成的一维数组后，输出一个有10个元素的一维数组。现在我们考虑打包输入多张图片的情况。比如我们想做预测，一次性输出100张图片的结果。为此，可以将100张图片打包作为输入数据。

![20](https://img.imgdb.cn/item/60311eff5f4313ce25b8c565.png)

这种打包式的输入数据成为**批**（batch）。

> 批处理对计算机的运算大有利处，可以大幅缩短每张图像的处理时间。这是因为大多数处理数值计算的库都进行了能够高效处理大型数组运算的优化。并且，在神经网络的运算中，当数据传送作为瓶颈时，批处理可以减轻数据总线的负荷（严格地讲，相对于数据读入，可以将更多的时间用在计算上）。也就是说，批处理一次性计算大型数组要比分开逐步计算各个小型数组速度更快。

第一部分就讲这么多了。
