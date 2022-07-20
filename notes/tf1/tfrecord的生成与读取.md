### 一、背景
#### 1.1 tfrecord是什么？
TFRecord 是 TensorFlow 自带的一种基于protobuffer的数据格式，也是TensorFlow 推荐的数据保存格式，其读取操作更加高效。
tfrecord作为一种较为通用的存储格式，和csv，parquet，json等文件格式属于同一个level的概念。TFRecord 格式是一种用于存储二进制记录序列的简单格式。
- [protobuffer详解](https://zhuanlan.zhihu.com/p/432875529)
- [TFRecord 和 tf.Example官网教程]https://tensorflow.google.cn/tutorials/load_data/tfrecord

#### 1.2 tfrecord相对于csv这些更常见的数据存储格式的好处是什么？
我们训练模型，加载数据一般有两种：
- 直接全部加载到内存中， 使用 placeholder 占位符。
- 使用 queue / tf.data (更推荐这种方式) 读硬盘中的数据
而在硬盘数据读取上，使用 TFRecod 读取数据更加的快，这是它最主要的优点.具体来说：
1. 更高效的存储：TFRecord 数据可以比原始数据占用更少的空间；它也可以分割成多个文件从而便于并行读取；
2. 快速 I/O：TFRecord 格式可以通过并行 I/O 操作读取，这对于TPU或分布式场景很有用，当然，即使是单线程读取，tfrecord的效率也是很高的，这一点上类似于feather，feather也是使用二进制来记录数据的。
3. 包括万象：tfrecord可以将不同类型的数据统一存储在一起，例如对于一个sample而言，它的文本，图像，tabular，语音等features可以统一存储为一个tfrecord的数据记录。

和csv这类文件格式不同的地方在于，为了高效地读取数据，对于一个超大的数据集，tfrecord往往并不只存储一个文件，而是对数据进行序列化并将其存储在一组可线性读取的文件（一般每个文件 100-200MB）中。这尤其适用于通过网络进行流式传输的数据。这种做法对缓冲任何数据预处理也十分有用。这意味着在csv中，一个数据集对应一个文件，而在tfrecord中，一个数据集可能对应多个tfrecord files。


### 二、TFRecord生成
#### 2.1 创建 tfrecords 文件
```
writer = tf.python_io.TFRecordWriter("./1.tfrecords")
for i in range(200):  # 假设我们在一个文件中保存200个Example
    example = tf.train.Example(...)
    writer.write(example.SerializeToString())
writer.close()
```

参考：
1. [[数据读取]1.tfRecord的生成和读取](https://zhuanlan.zhihu.com/p/462458423)
2. [tf_record 的几种生成方法。](https://www.jianshu.com/p/26b331bfb393)
3. [tfrecord和tfrecorder](https://zhuanlan.zhihu.com/p/505014740)
4. [TFRecord 的保存与读取（保姆级教程）](https://zhuanlan.zhihu.com/p/363999842)


