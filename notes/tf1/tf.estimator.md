
tf.estimator 是一种高级Tensorflow API，使用Estimators编写应用程序时，必须将数据输入与模型分开。

![image](https://user-images.githubusercontent.com/29428195/179508220-d99d5b9e-18e5-4c63-a554-687b92b9f925.png)

### 一.准备数据+格式标准化(input_fn)：
input_fn, 每次为1个batch_size的大小数据
```
def input_fn(dataset):
    ...
    return feature_dict,label
```
格式：元组 ( features , labels )
其中 features 和 labels 均必须为Tensor 或 以string为key，以Tensor为value的字典。

一般多为：features 是以string为key，以Tensor为value的字典； labels是Tensor。

可以使用其提供的预制估算器（pre-made Estimators），也可以编写自己的自定义估算器（custom Estimators）
