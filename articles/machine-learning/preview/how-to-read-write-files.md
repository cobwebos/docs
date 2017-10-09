---
title: "如何读取和写入大型数据文件 | Microsoft Docs"
description: "如何在 Azure ML 试验中读取和写入大型文件。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fb3158ef786ad73440a59c07b38476a98ced0768
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="persisting-changes-and-dealing-with-large-files"></a>持久保存更改和处理大型文件

## <a name="execution-isolation-portability-and-reproducibility"></a>执行隔离、可移植性和可再现性
使用 Azure ML 试验服务可以配置各种执行目标，有些是本地目标，比如本地计算机或本地计算机上的 Docker 容器，有些则是远程目标，比如远程计算机中的 Docker 容器或 HDInsight 群集。 请参考[配置试验执行](experiment-execution-configuration.md)一文了解更多详细信息。 在进行任何执行前，必须先将项目文件夹复制到该计算目标中。 即使进行本地执行也是如此，但这种情况下会使用本地临时文件夹。 

此设计的目的是确保执行隔离、可再现性和可移植性。 如果在同一计算目标或不同计算目标中对同一脚本执行两次，可保证收到相同的结果。 第一次执行所做的更改不应影响第二次执行。 在此设计中，我们可以将计算目标视为与已完成执行的作业无关联的无状态计算资源。

## <a name="challenges"></a>挑战
此设计既具有可移植性和可再现性优势，也带来一些独特的挑战：
### <a name="persisting-state-changes"></a>持久保存状态更改
如果脚本在计算上下文中修改了状态，那么，既不会为下一次执行持久保存更改，也不会自动将更改传播回客户端计算机。 

更具体地说，如果脚本创建一个子文件夹，或写出某个文件，那么，在项目目录后期执行中将找不到此类文件夹或文件。 它们会保存在当时所处计算目标环境中的某个临时文件夹上。 可以将它们用于调试，但永远不能指望它们一直存在。

### <a name="dealing-with-large-files-in-project-folder"></a>处理项目文件夹中的大型文件

如果项目文件夹包含任何大型文件，则在每次开始执行，将项目文件夹复制到目标计算环境时，都会有延迟。 即使在本地执行，它也会带来不必要的磁盘流量，这是应当避免的。 这就是为什么我们现在会制定一个 25 MB 的项目大小上限。

## <a name="option-1-use-the-outputs-folder"></a>选项 1：使用输出文件夹
如果脚本生成文件，用户预计在每次运行试验时这些文件都会发生更改，并且想要保留这些文件的历史记录，则此选项是首选选项。 常见用例为，用户在为模型定型、创建数据集，或在模型定型执行过程中将图绘制成图像文件时， 希望比较所有运行的这些输出，并且希望返回并选择上次运行生成的输出文件（比如模型），将其用于后续任务（比如评分）。

实质上，可以将文件写入根目录下名为 `outputs` 的文件夹。 这是一个用于接收试验服务的特殊处理的特殊文件夹。 运行完成后，脚本在执行期间于此处创建的任何内容（比如模型文件、数据文件或绘制的图像文件（统称为_项目_）都会复制到与试验帐户关联的 Azure Blob 存储帐户。 它们会成为运行历史记录的一部分。

下面是一个将模型存储到 `outputs` 文件夹的简单示例：
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
可以下载任何_项目_，方法如下：浏览到 Azure ML Workbench 中某个特定运行的运行详细信息页的“输出文件”部分，选择该项目，然后单击“下载”按钮。 也可以从 CLI 窗口使用 `az ml asset download` 命令。

若要查看更完整的示例，请参阅 _Iris 分类_示例项目中的 `iris_sklearn.py` Python 脚本。

## <a name="option-2-use-the-shared-folder"></a>选项 2：使用共享文件夹
在下列情况中，如果不需要为每次运行保留这些文件的历史记录，则使用可以跨独立运行访问的共享文件夹会是一个很好的选择： 
- 脚本需要从本地文件（比如 csv 文件，或者文本或图像文件的目录）加载数据，作为定型或测试数据。 
- 脚本处理原始数据，并写出中间结果（比如文本/图像文件中的特征化定型数据）用于后续定型运行。 
- 脚本创建一个模型，后续评分脚本需要选取该模型用于评估。 

特别需要注意的是，共享文件夹位于所选计算目标本地。 因此，仅当引用共享文件夹的所有脚本运行在同一个计算目标上执行，并且该计算目标不在两次运行之间循环使用时，才能使用此共享文件夹。

共享文件夹功能可用于在由环境变量 `AZUREML_NATIVE_SHARE_DIRECTORY` 标识的特殊文件夹中进行读取或写入。 

### <a name="example"></a>示例
下面是一段使用此共享文件夹读取和写入文本文件的示例 Python 代码：
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

有关更完整的示例，请参阅 _Iris 分类_示例项目中的 `iris_sklearn_shared_folder.py` 文件。

在使用此功能之前，必须先在 `aml_config` 文件夹中表示目标执行上下文的 `.compute` 文件中设置一些简单配置。 此文件夹的实际路径可能有所不同，具体取决于所选的计算目标和配置的值。

### <a name="configure-local-compute-context"></a>配置本地计算上下文

若要在本地计算上下文中启用此功能，只需向表示_本地_环境的 `.compute` 文件（通常命名为 `local.compute`）添加以下行。
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

`~/.azureml/share` 是默认的基本文件夹路径。 可以将它更改为可供脚本运行访问的任意本地绝对路径。 试验帐户名称、工作区名称和项目名称自动追加到基目录，构成共享目录的完整路径。 例如，如果使用前面的默认值，则可以将文件写入到以下路径（并从中检索文件）：

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-docker-compute-context-local-or-remote"></a>配置 Docker 计算上下文（本地或远程）
若要在 Docker 计算上下文中启用此功能，需要向本地或远程 Docker _.compute_ 文件添加以下两行。

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>使用 `AZUREML_NATIVE_SHARE_DIRECTORY` 环境变量访问共享文件夹时，`sharedVolume` 必须设置为 `true`，否则执行将失败。

Docker 容器中运行的代码始终将此共享文件夹看作 `/azureml-share/`。 Docker 容器看到的此文件夹路径是不可配置的。 在代码中不应依赖此文件夹名称， 而应始终使用环境变量名称 `AZUREML_NATIVE_SHARE_DIRECTORY` 寻址此文件夹。 它会映射到 Docker 主机/计算上下文中的本地文件夹。 此本地文件夹的基目录是 `.compute` 文件中 `nativeSharedDirectory` 设置的可配置值。 主机上共享文件夹的本地路径（如果使用上面的默认值）如下所示：
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!TIP]
>请注意，在本地计算上下文和本地 Docker 计算上下文之间，共享文件夹在本地磁盘上的路径是相同的。 这意味着甚至可以在本地运行和本地 Docker 运行之间共享文件。

可以直接将输入数据放在这些文件夹中，该计算机上的本地运行或 Docker 运行将会选取它们。 也可以从本地运行或 Docker 运行将文件写入此文件夹，文件将会持久保存在该文件夹中，一直到执行生命周期结束。

有关 Azure ML 执行服务中配置文件的详细信息，请参考此文：[执行配置文件](experiment-execution-configuration-reference.md)。

>[!NOTE]
>HDInsight 计算上下文不支持 `AZUREML_NATIVE_SHARE_DIRECTORY` 环境变量。 但是，通过显式使用 WASB 绝对路径在附加 Blob 存储中进行读取/写入，可轻松实现相同的目的。

## <a name="option-3-use-external-durable-storage"></a>选项 3：使用外部持久存储

用户当然可以随意使用外部持久存储在执行期间持久保存状态。 此选项适用于以下情况：
- 输入数据已存储在可从目标计算环境访问的持久存储中。
- 这些文件无需成为运行历史记录的一部分。
- 这些文件需要由不同计算环境中的执行共享。
- 这些文件需要在计算上下文本身持久保存。

从 Python/PySpark 代码使用 Azure blob 存储就是一个例子。

下面是一个从 Python 代码使用 Azure blob 存储的简单示例：
```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

下面还有一个将任意 Azure Blob 存储附加到 HDI Spark 运行时的简单示例：
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>结束语
由于 Azure ML 通过将整个项目文件夹复制到目标计算上下文来执行脚本，因此对于大型输入、输出和中间文件必须格外小心。 对于大型文件事务，可以使用特殊的 `outputs` 文件夹、可通过 `AZUREML_NATIVE_SHARE_DIRECTORY` 环境变量访问的共享文件夹或外部持久存储。 

## <a name="next-steps"></a>后续步骤
- 请查看[配置试验执行](experiment-execution-configuration-reference.md)。
- 请参阅 [Iris 分类](tutorial-classifying-iris-part-1.md)教程项目如何使用 `outputs` 文件夹持久保存定型模型。
