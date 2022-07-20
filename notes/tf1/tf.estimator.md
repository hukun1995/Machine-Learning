
tf.estimator 是一种高级Tensorflow API，使用Estimators编写应用程序时，必须将数据输入与模型分开。

![image](https://user-images.githubusercontent.com/29428195/179508220-d99d5b9e-18e5-4c63-a554-687b92b9f925.png)

### 一.准备数据+格式标准化(input_fn)：
input_fn, 每次为1个batch_size的大小数据，用于给定模型所需的数据
```
def input_fn(dataset):
    ...
    return feature_dict,label
```
格式：元组 ( features , labels )，一般多为：features 是以string为key，以Tensor为value的字典； labels是Tensor。


**举例：用tf.estimator.inputs.numpy_input_fn**
```
x_train = np.array([1., 2., 3., 6., 8.])
y_train = np.array([4.8, 8.5, 10.4, 21.0, 25.3])
input_fn = tf.estimator.inputs.numpy_input_fn({"x":x_train},y_train,batch_size=2,num_epochs=None,shuffle=True)
# batch_size每批数据个数,shuffle是否打乱数据,num_epochs为整个数据的排列次数，训练集所有数据过一遍为1次epoch
#注意，对于任何操作，当输入的input_fn中num_epochs=None时，要注意设置steps=,否则会无限步操作

#声明数据特征格式，创建模型实例
feature_columns = [tf.feature_column.numeric_column("x",shape=[1])]#特征向量：1个元素的数组，命名x
estimator = tf.estimator.LinearRegressor(feature_columns=feature_columns)#线性回归训练器
```






参考：
1. [tf.estimator讲解：数据和估算器定义](https://zhuanlan.zhihu.com/p/436766369)
