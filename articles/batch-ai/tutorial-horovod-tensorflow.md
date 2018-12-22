---
title: 教程 - 使用 Azure Batch AI 和 Horovod 进行分布式训练 | Microsoft Docs
description: 教程 - 如何使用 Azure Batch AI 服务和 Azure CLI 通过 Horovod 训练分布式模型。
services: batch-ai
author: johnwu10
manager: jeconnoc
ms.service: batch-ai
ms.topic: tutorial
ms.date: 09/03/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 45255845d8645391ee33471830ac2ef27870a40d
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408623"
---
# <a name="tutorial-train-a-distributed-model-with-horovod"></a>教程：通过 Horovod 训练分布式模型

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

在本教程中，请在 Batch AI 群集中跨多个节点并行运行分布式深度学习模型，以便对其进行训练。 Batch AI 是一项托管服务，可以在 Azure GPU 群集上大规模训练机器学习和 AI 模型。 

本教程介绍了一个常用 Batch AI 工作流，并介绍了如何通过 Azure CLI 与 Batch AI 资源交互。 涵盖的主题包括：

> [!div class="checklist"]
> * 设置 Batch AI 工作区、试验和群集
> * 为输入和输出设置 Azure 文件共享
> * 通过 Horovod 并行化深度学习模型
> * 提交训练作业
> * 监视作业
> * 检索训练结果

在本教程中，将会修改对象检测模型，使之能够通过 [Horovod](https://github.com/uber/horovod) 并行运行。 此模型在图像的 [CIFAR-10 数据集](https://www.cs.toronto.edu/~kriz/cifar.html)上训练。 此训练作业在包含 24 个 vCPU 和 4 个 GPU 的群集上运行，需要大约 60 分钟才能完成。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.38 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="why-use-horovod"></a>为何使用 Horovod？

Horovod 是一个适用于 Tensorflow、Keras 和 PyTorch 的分布式训练框架，因此用于本教程。 使用 Horovod 时，只需数行代码即可将设计用于在单个 GPU 上运行的训练脚本转换为可在分布式系统上高效运行的脚本。

除了 Horovod，Batch AI 还支持将分布式训练与多个其他的常用开源框架配合使用。 请务必查看任何用于在生产环境中训练模型的框架的许可条款。

## <a name="prepare-the-batch-ai-environment"></a>准备 Batch AI 环境

### <a name="create-a-resource-group"></a>创建资源组

使用 `az group create` 命令在 `eastus` 区域创建名为 `batchai.horovod` 的资源组。 使用该资源组来部署 Batch AI 资源。

```azurecli-interactive
az group create --name batchai.horovod --location eastus
```

### <a name="create-a-workspace"></a>创建工作区

使用 `az batchai workspace create` 命令创建 Batch AI 工作区。 工作区是一种顶级集合，包含其他 Batch AI 资源。 以下命令在资源组下创建名为 `batchaidev` 的工作区。

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

### <a name="create-an-experiment"></a>创建试验

Batch AI 试验可将一个或多个可以一起查询和管理的作业组合起来。 以下 `az batchai experiment create` 命令在工作区和资源组下创建名为 `cifar` 的试验。

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

## <a name="set-up-a-gpu-cluster"></a>设置 GPU 群集

接下来，设置用于运行试验的 GPU 群集。 Batch AI 提供范围灵活可变的选项，可以针对特定需求来自定义群集。

以下 `az batchai cluster create` 命令在工作区和资源组下创建名为 `nc6cluster` 的 4 节点群集。 默认情况下，群集中的 VM 运行一个 Ubuntu Server 映像，该映像旨在托管基于容器的应用程序。 此示例中的群集节点使用 `Standard_NC6` 大小，该大小包含一个 NVIDIA Tesla K80 GPU。

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --workspace batchaidev --name nc6cluster --vm-priority dedicated  --vm-size Standard_NC6 --target 4 --generate-ssh-keys
```

请运行 `az batchai cluster show` 命令以查看群集状态。 群集的完全预配通常需要数分钟。

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

在群集创建初期，群集处于`resizing`状态。 在群集状态更改时，继续以下步骤。 当群集状态为`steady`且节点处于`idle`状态时，群集就可以运行训练作业。 例如：

```
Name        Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
----------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
nc6cluster  batchai.horovod  batchaidev   STANDARD_NC6  steady        4          0            0          0           0
```

## <a name="set-up-storage"></a>设置存储

使用 `az storage account create` 命令创建一个存储帐户，以便存储训练脚本和训练输出。

```azurecli-interactive
az storage account create --resource-group batchai.horovod --name mystorageaccount --location eastus --sku Standard_LRS
```

使用 `az storage share create` 命令在帐户中创建名为 `myshare` 的 Azure 文件共享：

```azurecli-interactive
az storage share create --name myshare --account-name mystorageaccount
```

实际上，这同一个存储可以用在多个作业和试验中。 为了便于组织，请在文件共享中创建一个目录，用于存储与这个特定的试验相关的文件。 以下 `az storage directory create` 命令创建名为 `cifar` 的目录。

```azurecli-interactive
az storage directory create --name cifar --share-name myshare --account-name mystorageaccount
```

下一步是准备实际的训练脚本，然后将其上传到新创建的目录。

## <a name="create-the-training-script"></a>创建训练脚本

在此试验中，请运行一个使用一些更改进行更新的 Python 脚本，以便使用 Horovod 并行运行对象检测模型。 [原始模型](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py)将 Keras 与 TensorFlow 后端配合使用。 

在 shell 的工作目录中，使用最常用的文本编辑器创建名为 `cifar_cnn_distributed.py` 的文件，其中包含以下内容。 对原始源代码所做的更改进行注释时，注释带有 `HOROVOD` 前缀。

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

如此示例所示，只有一些对模型所做的更新需要启用通过 Horovod 框架进行的分布式训练。 

请注意，此脚本使用相对较小的模型和数据集进行演示，因此此分布式模型不一定会显著改善性能。 若要真正见识分布式训练的“威力”，需使用大得多的模型和数据集。

## <a name="upload-the-training-script"></a>上传训练脚本

脚本准备就绪后，下一步是将它上传到此前创建的文件共享目录。 以下 `az storage file upload` 命令将它从本地工作目录上传到适当的位置。

```azurecli-interactive
az storage file upload --path cifar --share-name myshare --source cifar_cnn_distributed.py --account-name mystorageaccount
```

## <a name="submit-the-training-job"></a>提交训练作业

完成前述步骤以后，创建一个训练作业。 在 Batch AI 中，请使用 `job.json` 文件来定义那些控制作业运行方式的参数。 使用最常用的文本编辑器创建一个名为 `job.json` 的作业配置文件，其中包含以下内容。

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

属性说明：

| 属性 | Description |
| --------- | --------- |
| `nodeCount` | 专用于作业的节点数。 在这里，作业会在 `4` 节点上并行运行。 |
| `horovodSettings` | `pythonScriptFilePath` 字段定义 Horovod 脚本的路径，该脚本位于此前创建的 `cifar` 目录中。 `commandLineArgs` 字段是用于运行脚本的命令行参数。 就此试验来说，保存模型时所在的目录是唯一必需的参数。 `$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare` 是装载文件共享时所在的路径。 | 
| `stdOutErrPathPrefix` | 用于存储作业输出和日志的路径。就此示例来说，它是同一个 `cifar` 目录。 |
| `jobPreparation` | 任何特殊说明，说明如何准备适用于运行作业的环境。 此脚本要求安装指示的 MPI 和 Horovod 包。 |
| `containerSettings` | 运行作业时所在容器的设置。 此作业使用通过 `tensorflow` 生成的 Docker 容器。

使用此配置通过 `az batchai job create` 命令创建作业。 以下命令对名为 `cifar_distributed` 的新作业排队，使用的所有资源都是到目前为止设置好的。 

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name mystorageaccount
```

如果节点当前处于忙的状态，则作业可能需要等待一会儿才能开始运行。 使用 `az batchai job show` 命令查看作业的执行状态。

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

### <a name="visualize-the-distributed-training"></a>可视化分布式训练

作业开始运行以后，请再次使用 `az batchai cluster show` 命令来查询群集节点的状态。 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

输出应如下所示，显示所有四个节点都处于运行状态。 此结果表明所有四个节点目前都在分布式训练中使用。

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## <a name="monitor-the-job"></a>监视作业

### <a name="list-output-files"></a>列出输出文件

当作业正在运行时，请使用 `az batchai job file list` 命令列出作业生成的输出文件。

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

就这个特定的试验来说，输出应如下所示。 作业的总体输出记录到 `stdout.txt`，而 `stderr.txt` 则会输出在主执行操作中发生的任何错误。 其他文件是与各个节点对应的输出、错误和作业准备日志。

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

### <a name="stream-an-output-file"></a>流式传输输出文件

使用 `az batchai job file stream` 命令流式传输文件内容。 以下示例流式传输主输出日志。

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

作业运行时，此命令会流式传输训练作业的标准输出，如下所示。

```
...
50000 train samples
10000 test samples
Using real-time data augmentation.
Epoch 1/25


   1/1563 [..............................] - ETA: 2:42:25 - loss: 2.3334 - acc: 0.0312   1/1563 [..............................] - ETA: 2:30:42 - loss: 2.2973 - acc: 0.0938
   1/1563 [..............................] - ETA: 30:36 - loss: 2.3175 - acc: 0.1250
   1/1563 [..............................] - ETA: 2:32:58 - loss: 2.3489 - acc: 0.0625
   2/1563 [..............................] - ETA: 1:21:59 - loss: 2.3230 - acc: 0.0625

   2/1563 [..............................]   2/1563 [..............................] - ETA: 1:16:09 - loss: 2.2913 - acc: 0.0938 - ETA: 1:17:15 - loss: 2.3147 - acc: 0.0781
   2/1563 [..............................] - ETA: 16:07 - loss: 2.3678 - acc: 0.0938
   3/1563 [..............................] - ETA: 55:05 - loss: 2.3232 - acc: 0.0938  
   3/1563 [..............................] - ETA: 51:57 - loss: 2.3185 - acc: 0.1146  
   3/1563 [..............................] - ETA: 51:12 - loss: 2.3179 - acc: 0.1042  
   3/1563 [..............................] - ETA: 11:13 - loss: 2.3504 - acc: 0.0833
   4/1563 [..............................] - ETA: 39:43 - loss: 2.3224 - acc: 0.1094
   4/1563 [..............................] - ETA: 42:09 - loss: 2.3049 - acc: 0.1250
   4/1563 [..............................] - ETA: 39:15 - loss: 2.3089 - acc: 0.1094
   4/1563 [..............................] - ETA: 9:16 - loss: 2.3316 - acc: 0.1016 
   5/1563 [..............................] - ETA: 39:51 - loss: 2.3153 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:58 - loss: 2.3197 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:35 - loss: 2.3148 - acc: 0.1062
   5/1563 [..............................] - ETA: 13:38 - loss: 2.3263 - acc: 0.1062
   6/1563 [..............................] - ETA: 35:48 - loss: 2.3168 - acc: 0.1198

   6/1563 [..............................]   6/1563 [..............................] - ETA: 34:13 - loss: 2.3142 - acc: 0.1198 - ETA: 33:51 - loss: 2.3162 - acc: 0.1042
   6/1563 [..............................] - ETA: 13:54 - loss: 2.3225 - acc: 0.1094
   7/1563 [..............................] - ETA: 30:53 - loss: 2.3181 - acc: 0.1071

   7/1563 [..............................]   7/1563 [..............................] - ETA: 29:32 - loss: 2.3149 - acc: 0.1161 - ETA: 29:13 - loss: 2.3140 - acc: 0.0938
   7/1563 [..............................] - ETA: 12:09 - loss: 2.3174 - acc: 0.1205
   8/1563 [..............................] - ETA: 26:04 - loss: 2.3113 - acc: 0.1133
   8/1563 [..............................] - ETA: 27:15 - loss: 2.3169 - acc: 0.1133
   8/1563 [..............................] - ETA: 10:51 - loss: 2.3152 - acc: 0.1172
...
```

此脚本训练 25 个以上的时期，或者贯穿整个训练数据集。 此过程大约需要 60 分钟。 

## <a name="retrieve-the-results"></a>检索结果

脚本完成时，如果一切正常，则验证准确度会达到大约 70-75%，训练的模型会保存到 `cifar/saved_models/keras_cifar10_trained_model.h5` 中的文件共享。 

模型训练通常是更大型工作流的一部分。 例如，可能会在另一应用程序中公开训练的模型。 若要将训练的模型下载到本地，请使用 `az storage file download` 命令。 

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name myshare --account-name mystorageaccount
```
## <a name="clean-up-resources"></a>清理资源

作业运行完以后，若要节省计算费用，最佳做法是将所有群集都缩减到 `0 nodes` 的规模，这样就不需为空闲时间付费。 使用以下 `az batchai cluster resize` 命令。 

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

随后请根据运行作业的需要，将其大小重设为 1 个或多个节点。 

如果不打算在将来使用工作区和存储帐户，请通过 `az group delete` 命令删除资源组。 删除资源组会删除该组中包含的所有资源。

```azurecli-interactive
az group delete --name batchai.horovod
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 设置 Batch AI 工作区、试验和群集
> * 为输入和输出设置 Azure 文件共享
> * 通过 Horovod 并行化模型
> * 提交训练作业
> * 监视作业
> * 检索训练结果

若要通过示例来了解如何将 Batch AI 与不同框架配合使用，请参阅 GitHub 上的方案。

> [!div class="nextstepaction"]
> [Batch AI 方案](https://github.com/Azure/BatchAI/tree/master/recipes)
