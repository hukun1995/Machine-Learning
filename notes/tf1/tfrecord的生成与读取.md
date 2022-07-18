### 一、背景
利用tensorflow训练深度模型，数据的处理和输入是必须的一个步骤，为了高效的读取数据，用官网文档的话说就是tfrecord对数据进行序列化并将其存储在一组可线性读取的文件中，从硬盘流式读取。
对于内存的利用有很大的作用。


### 二、TFRecord生成
**数据说明**
工程上一般数据输入是从hive表拉取的文件列表，整理成数据文件列表形式，以文件列表输入，tfrecord文件列表输出。一般要点包括
1. 多线程读取解析。
2. tfrecord写入函数
3. tfrecord行字段解析

```
from multiprocessing import Pool
import os
import glob
import numpy as np
import pandas as pd
 
//（3）tfrecord行字段解析	
def parse_line(line):
    feature_list = line.strip('\n').split('\t')
    features = dict()
    feature.update({'fea1':tf.train.Feature(float_list=tf.train.FloatList(value=np.array(feature_list[0],dtype=np.float)))})
    feature.update({'fea2':tf.train.Feature(bytes_list=tf.train.BytesList(value=feature_list[1]))})
 
    feature.update({'label':tf.train.Feature(int64_list=tf.train.Int64List(value=feature_list[2]))})
    return features
 
//（2）tfrecord写入函数
def tfrecord_process(params):
    infile_name = params[0]
    output_dir = params[1]
	
    outfile_name = f'{output_dir}.tfrecord'
    tfrecord_out = tf.io.TFRecordWriter(outfile_name) //定义输出文件
    with open(infile_name, 'r', encoding='utf-8') as fp:
        for line in fp.readlines():
            feat = parse_line(line) //（3）tfrecord行字段解析
            example = tf.train.Example(features=tf.train.Features(feature=feat))
            serialized = example.SerializeToString()
            tfrecord_out.write(serialized)
    tfrecord_out.close()
 
//（1）多线程读取解析。
if __name_ == "__main__":
    //定义多线程读取
    input_dir = args.input_dir
    output_dir = args.output_dir
    input_files = glob.glob(input_dir)
    parameters = [[item, output_dir] for item in input_files]
 
    pool = Pool(args.threads)
    pool.map(tfrecord_process, parameters)
    pool.close()
    pool.join()
```

函数解释
example = tf.train.Example(features=tf.train.Features(feature=feat))
参数：features（字典key-value形式，key是特征名字，value是特征值），value=tf.train.Feature()
tf.train.feature属性，每一个feature 是一个key-value的键值对，其中，key 是string类型，value 的取值有三种：
* bytes_list: 可以存储string 和byte两种数据类型。
* float_list: 可以存储float(float32)与double(float64) 两种数据类型 。
* int64_list: 可以存储：bool, enum, int32, uint32, int64, uint64。
————————————————
版权声明：本文为CSDN博主「感冒灵pp」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。


参考：
1. [[数据读取]1.tfRecord的生成和读取]([https://blog.csdn.net/lht_okk/article/details/122929430](https://zhuanlan.zhihu.com/p/462458423))



