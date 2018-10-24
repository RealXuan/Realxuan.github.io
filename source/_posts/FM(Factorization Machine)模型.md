---
title: FM(Factorization Machine)模型
date: 2018-10-18
categories:  
    - 推荐与CTR
mathjax: true
---
> 导语：FM是由Steffen Rendle于2010年提出的一种基于矩阵分解的机器学习模型，解决了CTR预估任务中稀疏数据的特征组合问题，在推荐和计算广告领域中得到广泛应用。本文将从背景、原理以及基于TensorFlow的代码实现三方面详细介绍FM模型。

## 1.FM的背景
在CTR预估的问题中存在大量的Categorical特征，我们通常的处理方法是对Categorical特征进行one-hot编码处理成0-1的二值特征，以保证不同Categorical特征之间的距离一致。形如：
![upload successful](/images/1018_pic1.PNG)
可以看出，特征不同的取值个数决定了one-hot编码之后特征维度，并且其中只有1维特征是1，其余的都是0。所以one-hot编码会使得特征变得高维并且稀疏，很难进行特征组合得到高阶的交叉特征。那为什么要进行特征组合呢？实际上，大量的特征之间存在潜在的关联。举个简单例子，女性用户倾向于点击化妆品、服装类型的物品，而男性用户则倾向于点击运动、电子类的物品，所以相比一阶特征，性别交叉物品种类的二阶特征会对CTR预估有更重要的作用。

<!--more-->

## 2.FM的原理
一般的线性模型 没有考虑特征组合，FM模型在线性模型的基础上增加二阶交叉特征，模型如下：
![upload successful](/images/1019_pic1.PNG)
然而针对大规模的稀疏数据，这样的模型还存在一些问题。从模型的表达式可以看出，$x_i\cdot x_j$是两个不同特征向量的内积，只有当$x_i$和$x_j$都不为0时内积才有意义，所以大量的稀疏数据会导致模型计算复杂度过高，很难训练得到$w_{i,j}$。
为了得到$w_{i,j}$，FM利用矩阵分解的思想，将交叉项系数矩阵W分解成两个低秩矩阵：$W=VV^T$
![upload successful](/images/1019_pic2.PNG)
$w_{i,j} = v_i v_{j}`{T}$，所以二阶交叉特征部分变形为：
![upload successful](/images/1019_pic3.PNG)
由于必须将$x_i\cdot x_j$分开才能有效地利用稀疏数据，所以FM对表达式做了进一步的化简：
![upload successful](/images/1019_pic4.PNG)
这步化简如何来的呢？其实不难发现等式的左边就是n*n阶对称矩阵的上三角部分，它的面积恰好等于矩形减掉对角线之后的一半。
![upload successful](/images/1019_pic5.PNG)
由于$\left< v_i,v_j \right>$等于$\sum_{f=1}^{k}v_{i,f}v_{j,f}$，所以展开后公式为：
![upload successful](/images/1019_pic6.PNG)
FM的表达式经过变形之后，所有包含非零$x_i$的组合样本都可以用来学习$v_i$,这在很大程度上避免了数据稀疏造成参数估计不准确的影响。
用梯度下降法进行模型训练时需要求预测值y_对各个参数的偏导数：
![upload successful](/images/1019_pic7.PNG)
模型的参数从$\frac{n(n-1)}{2}$减少为nk+n+1，模型的计算复杂度从$O \left( n^{2} \right)$降低到$O \left( nk \right)$，综上FM是一个针对大规模稀疏数据的高效预测模型。

##3.基于TensorFlow的FM实现
``` python
class FM(Model):
	def __init__(self,input_dim=None,output_dim=1,factor_orider=10,opt_algo='gd',learning_rate=le-2,l2_w=0,l2_v=0,random_seed=23):
		Model.__init__(self)	
	    #定义计算图
		self.graph = tf.Graph()
	 	with self.graph.as_default():
			if random_seed is not None:
				tf.set_random_seed(random_seed)
			#定义参数节点
			self.X = tf.sparse_placeholder(tf.float32, shape=[None, input_dim], name="X")
			self.y = tf.placeholder(tf.float32, shape=[None, output_dim], name="y")
			elf.V = tf.get_variable("v", shape=[input_dim, factor_orider], dtype=tf.float32, initializer=tf.truncated_normal_initializer(stddev=0.3))
			self.W = tf.get_variable("Weights", shape=[n, 1], dtype=tf.float32, initializer=tf.truncated_normal_initializer(stddev=0.3))
			self.b = tf.get_variable("Biases", shape=[1, 1], dtype=tf.float32, initializer=tf.zeros_initializer())
			#定义模型
			#一阶部分
			 xw = tf.sparse_tensor_dense_matmul(self.X, self.W)
			#二阶部分 multiply是矩阵对应元素相乘，matmul是矩阵乘法
			fm_hat = tf.reduce_sum(tf.square(tf.sparse_tensor_dense_matmul(self.X ,self.V)) - (tf.sparse_tensor_dense_matmul(tf.multiply(self.X,self.X), tf.multiply(self.V,self.V))), axis=1, keep_dims=True) / 2
			#fm函数
			logits = tf.reshape(b+xw+fm_hat,[-1])
			#通过sigmoid函数计算预测值
			self.y_ = tf.sigmoid(logits)
			#模型的损失函数：交叉熵损失函数l2正则
			self.loss = tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(logits=logits, labels=self.y)) +l2_w * tf.nn.l2_loss(xw) + l2_v * tf.nn.l2_loss(xv)
			#模型的优化器
			self.optimizer = get_optimizer(opt_algo, learning_rate, self.loss)
			#初始化模型参数
			with tf.Session() as sess:
				sess.run(tf.global_variables_initializer())

```
