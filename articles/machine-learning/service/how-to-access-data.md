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
ms.openlocfilehash: 615ab592c040eedf7d31e3a3036f558ea6c09740
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990801"
---
# <a name="how-to-access-data-during-training"></a>如何在定型期间访问数据
在 Azure 机器学习服务中，数据存储是 [Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-introduction)中的一个抽象化概念。 数据存储可以引用 [Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)或 [Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)作为基础存储。 有了数据存储，可通过 Python SDK 或 CLI 在 Azure 机器学习工作流期间轻松访问你的数据存储并与之交互。

## <a name="create-a-datastore"></a>创建数据存储
若要使用数据存储，首先需要一个[工作区](concept-azure-machine-learning-architecture.md#workspace)。 可以新建工作区或检索现有工作区：

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>使用默认数据存储
每个工作区都有一个可以立即开始使用的默认数据存储，可节省创建和配置你自己的存储帐户的工作。

若要获取工作区的默认数据存储：
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>注册数据存储
如果已有现有的 Azure 存储，可以将其注册为工作区上的数据存储。 Azure 机器学习提供了一项功能，可将 Azure Blob 容器或 Azure 文件共享注册为数据存储。 所有注册方法都位于 `Datastore` 类，并具有窗体 `register_azure_*`。

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
    print(name, ds.datastore_type)"
```

为方便起见，如果想要将其中一个已注册的数据存储设置为工作区的默认数据存储，可如下所述进行操作：
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>上传和下载数据
### <a name="upload"></a>上载
可以使用 Python SDK 将目录或单个文件上传到数据存储。

将目录上传到数据存储 `ds`:
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` 指定要上传的文件共享（或 blob 容器）中的位置。 默认设置为 `None`，在这种情况下数据将上传到根目录。 `overwrite=True` 将覆盖 `target_path` 中的任何现有数据。

或者，可以将单个文件的列表通过数据存储的 `upload_files()` 方法上传到数据存储。

### <a name="download"></a>下载
同样，可以将数据从数据存储下载到本地文件系统。

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` 是要将数据下载到的本地目录位置。 若要在文件共享（或 blob 容器）中指定要下载到的文件夹路径，请提供 `prefix` 的路径。 如果 `prefix` 是 `None`，将下载文件共享（或 blob 容器）的所有内容。

## <a name="access-datastores-for-training"></a>访问用于定型的数据存储
可以在定型通过 Python SDK 在远程计算目标上运行（例如，针对定型或验证数据）期间访问数据存储。 

可通过两种受支持的方式让数据存储在远程计算上可用：
* **装载**  
`ds.as_mount()`：通过指定此装载模式，数据存储将装载到远程计算上。 
* **下载/上传**  
    * `ds.as_download(path_on_compute='your path on compute')`：在此下载模式下，数据将从数据存储下载到 `path_on_compute` 指定的远程计算位置。
    * 相反，也可以将定型运行中生成的数据上传到数据存储。 例如，如果定型脚本在远程计算上的当前工作目录中创建了一个 `foo.pkl` 文件，可以在运行脚本后为其指定以下位置以上传到数据存储：`ds.as_upload(path_on_compute='./foo.pkl')`。 这会将文件上传到数据存储的根目录。
    
如果想要引用数据存储中的特定文件夹或文件，可以使用数据存储的 `path` 函数。 例如，如果数据存储具有包含相对路径 `./bar` 的目录，并且只想将此文件夹的内容下载到计算目标，可如下所述进行操作：`ds.path('./bar').as_download()`

任何 `ds` 或 `ds.path` 对象都将解析为格式 `"$AZUREML_DATAREFERENCE_XXXX"` 的环境变量名，其值表示远程计算上的装载/下载路径。 远程计算上的数据存储路径可能与该脚本的执行路径不同。

若要在定型期间访问数据存储，可以将其作为命令行参数通过 `script_params` 传入到定型脚本：

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

或者，可以将数据存储列表传递到 Estimator 构造函数的 `inputs` 参数，以装载或/复制到数据存储，或从数据存储进行装载/复制：

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
