# ResNet-Prototxt-for-Caffe

This script is used to creat ResNet prototxt on cifar10 for Caffe. Following the original paper, N = {3, 5, 7 , 9, 18} needs to be given, where
- 3  for 20-layer network
- 5  for 32-layer network
- 7  for 44-layer network
- 9  for 56-layer network
- 18 for 110-layer network

# Usage:

```
python resnet_generator.py training_data_path test_data_path mean_file_path N
```

- training_data_path: the path of training data (LEVELDB or LMDB).
-     test_data_path: the path of test data (LEVELDB or LMDB).
-     mean_file_path: the path of mean file for training data.
-                  N: a parameter introduced by the original paper, meaning the number of repeat of residualn building block for each feature map size (32, 16, 8). For example, N = 5 means that creat 5 residual building blocks for feature map size 32, 5 for feature map size 16, and 5 for feature map size 8. Besides, in each building block, two weighted layers are included. So there are (5 + 5 + 5)*2 + 2 = 32 layers.

# Examples: 

```
python resnet_generator.py ./training_data ./test_data ./mean.binaryproto 5
```

# Validation error rate

|  model  | my test error | test error from Tab.6 |
|---------|:-------------:|:---------------------:|
|resnet20 | 8.5           |      8.75             |
|resnet32 | 7.57          |      7.51             |
|resnet44 | 7.13          |      7.17             |
|resnet56 | 7             |      6.97             |

In these experiments, residual networks were trained on a preprocessed cifar10 dataset( GCN and ZCA) for 200 epochs( 80k iterations ). The initial learning rate was 0.1 and divided by 10 at 48k and 64k iterations, which is different from the origianl paper. For other settings, weight decay, momentum, batch size and data augmentation were the same as paper. I didn't use Nesterov momentum. 

### Nesterov momentum
I also tried Nesterov momentum on the resnet44 with other settings unchanged. 

|  model  | Newterov momentum | vanilla momentum |
|---------|:-----------------:|:----------------:|
|resnet44 |   7.35            |     7.13         |

### Comparison of BN stats used in Test phase
In some repositories, BN stats was set to `false` in both `Train` and `Test` phase. However, this will harm the accuracy.

|  model  | BN stats False in Test | BN stats True in Test |
|---------|:----------------------:|:---------------------:|
|resnet32 | 8.81                   |      8.11             |
|resnet44 | 8.41                   |      7.74             |
|resnet56 | 7.98                   |      7.36             |

All the settings were the same as the original paper: train for 64k iterations; learning rate starts at 0.1 and was divided by 10 at 32k and 48k iterations. Note that the final results of `BN stats True in Test` are slightly worse than the case of training for 200 epochs.
