---
title: 使用 Azure 机器学习中的数据存储访问数据
description: 如何在定型期间使用数据存储访问数据存储
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: ef183397cf7ca3f1f561f6951290b722df31e5ed
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620194"
---
# <a name="how-to-access-data-during-training"></a>如何在定型期间访问数据
使用数据存储可以访问 Azure 机器学习工作流中的数据并与之交互。

在 Azure 机器学习服务中，数据存储是基于 [Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-introduction)的一种抽象。 数据存储可以引用 [Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)或 [Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)作为基础存储。 

## <a name="create-a-datastore"></a>创建数据存储
若要使用数据存储，首先需要一个[工作区](concept-azure-machine-learning-architecture.md#workspace)。 首先，请[创建新工作区](quickstart-create-workspace-with-python.md)或检索现有的工作区：

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>使用默认数据存储
无需创建或配置存储帐户。  每个工作区具有一个可以立即开始使用的默认数据存储。

若要获取工作区的默认数据存储：
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>注册数据存储
如果已有现有的 Azure 存储，可将其注册为工作区中的数据存储。 可将 Azure Blob 容器或 Azure 文件共享注册为数据存储。 所有注册方法都位于 `Datastore` 类，并具有窗体 `register_azure_*`。

#### <a name="azure-blob-container-datastore"></a>Azure Blob 容器数据存储
注册 Azure Blob 容器数据存储：

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Azure 文件共享数据存储
注册 Azure 文件共享数据存储：

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>获取现有数据存储
按名称查询已注册的数据存储：
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

还可以为工作区获取所有数据存储：
```Python
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)
```

为方便起见，请将某个已注册的数据存储设置为工作区的默认数据存储：
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>上传和下载数据
### <a name="upload"></a>上载
使用 Python SDK 将目录或单个文件上传到数据存储。

将目录上传到数据存储 `ds`:
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` 指定要上传的文件共享（或 blob 容器）中的位置。 默认设置为 `None`，在这种情况下数据将上传到根目录。 `overwrite=True` 将覆盖 `target_path` 中的任何现有数据。

或者，将单个文件的列表通过数据存储的 `upload_files()` 方法上传到数据存储。

### <a name="download"></a>下载
同样，可将数据从数据存储下载到本地文件系统。

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` 是要将数据下载到的本地目录位置。 若要在文件共享（或 blob 容器）中指定要下载到的文件夹路径，请提供 `prefix` 的路径。 如果 `prefix` 是 `None`，将下载文件共享（或 blob 容器）的所有内容。

## <a name="access-datastores-for-training"></a>访问用于定型的数据存储
可以在通过 Python SDK 对远程计算目标执行训练运行（例如，针对训练或验证数据）期间访问数据存储。 

可通过两种受支持的方式让数据存储在远程计算上可用：
* **装载**  
`ds.as_mount()`：通过指定此装载模式，数据存储将装载到远程计算上。 
* **下载/上传**  
    * `ds.as_download(path_on_compute='your path on compute')` 将数据从数据存储下载到远程计算资源中由 `path_on_compute` 指定的位置。
    * `ds.as_upload(path_on_compute='yourfilename'` 将数据上传到数据存储。  假设培训脚本在远程计算资源上的当前工作目录中创建 `foo.pkl` 文件。 在脚本创建此文件后，使用 `ds.as_upload(path_on_compute='./foo.pkl')` 将此文件上传到数据存储。 此文件将上传到数据存储的根目录。
    
若要引用数据存储中的特定文件夹或文件，请使用数据存储的 `path` 函数。 例如，若要将数据存储中 `./bar` 目录的内容下载到计算目标，请使用 `ds.path('./bar').as_download()`。

任何 `ds` 或 `ds.path` 对象都将解析为格式 `"$AZUREML_DATAREFERENCE_XXXX"` 的环境变量名，其值表示远程计算上的装载/下载路径。 远程计算上的数据存储路径可能与该脚本的执行路径不同。

若要在训练期间访问数据存储，可以通过 `script_params` 将其作为命令行参数传入训练脚本：

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```
`as_mount()` 是数据存储的默认模式，因此还可以将 `ds` 直接传递给 `'--data_dir'` 参数。

或者，将数据存储列表传递到 Estimator 构造函数的 `inputs` 参数，以便与数据存储之间来回装载/复制数据：

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```
上面的代码会：
* 在运行定型脚本 `train.py` 之前，将数据存储 `ds1` 中的所有内容都下载到远程计算
* 在运行 `train.py` 之前，将数据存储 `ds2` 中的文件夹 `'./foo'` 下载到远程计算
* 在运行脚本后，将文件 `'./bar.pkl'` 从远程计算上传到数据存储 `d3`

## <a name="next-steps"></a>后续步骤
* [定型模型](how-to-train-ml-models.md)
