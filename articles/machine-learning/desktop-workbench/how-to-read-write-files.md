---
title: 读取和写入大型数据文件 | Microsoft Docs
description: 在 Azure 机器学习试验中读取和写入大型文件。
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: 3e7436c4b69a27931238ea80304231394074ffe3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831088"
---
# <a name="persisting-changes-and-working-with-large-files"></a>持久保存更改和处理大型文件
使用 Azure 机器学习试验服务可以配置各种执行目标。 有些目标是本地目标，例如本地计算机，或本地计算机上的 Docker 容器。 有些则是远程目标，例如远程计算机或 HDInsight 群集上的 Docker 容器。 有关详细信息，请参阅 [Azure 机器学习试验执行服务概述](experimentation-service-configuration.md)。 

在目标上执行之前，必须先将项目文件夹复制到计算目标。 即使为此使用本地临时文件夹进行本地执行，也必须这样做。 

## <a name="execution-isolation-portability-and-reproducibility"></a>执行隔离、可移植性和可再现性
此设计的目的是确保执行的隔离、可再现性和可移植性。 如果在相同或不同的计算目标上执行同一脚本两次，可获得相同的结果。 在第一次执行期间所做的更改不应影响第二次执行中的更改。 在此设计中，可将计算目标视为无状态计算资源，其中每个资源与完成后执行的作业无关联。

## <a name="challenges"></a>挑战
尽管此设计提供可移植性和可再现性优势，但也存在一些独特的难题。

### <a name="persisting-state-changes"></a>持久保存状态更改
如果脚本在计算上下文中修改了状态，那么，既不会为下一次执行持久保存更改，也不会自动将更改传播回客户端计算机。 

更具体地说，如果脚本创建了某个子文件夹或写入了某个文件，在执行后，该文件夹或文件不会出现在项目目录中。 这些文件会存储在计算目标环境中的某个临时文件夹内。 可将这些文件用于调试，但不能指望它们永久存在。

### <a name="working-with-large-files-in-the-project-folder"></a>处理项目文件夹中的大型文件

如果项目文件夹包含任何大型文件，则开始执行并将文件夹复制到目标计算环境时会发生延迟。 即使执行在本地发生，也仍要避免不必要的磁盘流量。 正因如此，我们目前将项目大小上限规定为 25 MB。

## <a name="option-1-use-the-outputs-folder"></a>选项 1：使用 *outputs* 文件夹
如果存在以下情况，则最好使用此选项：
* 脚本生成文件。
* 预期文件在每次试验中会发生更改。
* 希望保留这些文件的历史记录。 

常见用例包括：
* 训练模型
* 创建数据集
* 在模型训练执行过程中将图形绘制成图像文件 

此外，想要比较每次运行后的输出，选择上次运行生成的输出文件（例如模型），然后将其用于后续任务（例如评分）。

可将文件写入相对于根目录的名为 *outputs* 的文件夹。 试验服务会特殊对待此文件夹。 运行完成后，脚本在执行期间于此文件夹中创建的任何内容（例如模型文件、数据文件或绘制的图像文件（统称为“项目”）都会复制到与试验帐户关联的 Azure Blob 存储帐户。 这些文件会成为运行历史记录的一部分。

以下简短代码示例演示如何将模型存储到 *outputs* 文件夹中：
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
可在 Azure Machine Learning Workbench 中浏览到特定运行的运行详细信息页的“输出文件”部分，来下载任何项目。 只需选择该运行，然后选择“下载”按钮即可。 或者，也可以在命令行接口 (CLI) 窗口中输入 `az ml asset download` 命令。

有关更完整的示例，请参阅“鸢尾花分类”示例项目中的 `iris_sklearn.py` Python 脚本。

## <a name="option-2-use-the-shared-folder"></a>选项 2：使用共享文件夹
对于以下情况，如果不需要保留每个运行的文件的历史记录，使用可以跨独立运行访问的共享文件夹会是一个很好的选择： 
- 脚本需要从本地文件（例如 csv 文件，或者文本或图像文件的目录）加载数据，作为训练或测试数据。 
- 脚本处理原始数据，并写出中间结果（比如文本或图像文件中的特征化训练数据）用于后续训练运行。 
- 脚本创建一个模型，后续评分脚本必须选取该模型用于评估。 

必须注意，共享文件夹驻留在所选计算目标的本地。 因此，仅当引用共享文件夹的所有脚本运行在同一个计算目标上执行，并且该计算目标不在两次运行之间循环使用时，才能使用此选项。

利用共享文件夹功能，可以读取或写入由环境变量 `AZUREML_NATIVE_SHARE_DIRECTORY` 标识的特殊文件夹。 

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

有关更完整的示例，请参阅“鸢尾花分类”示例项目中的 *iris_sklearn_shared_folder.py* 文件。

在使用此功能之前，必须先在 *.compute* 文件中设置一些简单配置，用于在 *aml_config* 文件夹中表示目标执行上下文。 此文件夹的实际路径可能有所不同，具体取决于所选的计算目标和配置的值。

### <a name="configure-local-compute-context"></a>配置本地计算上下文

若要在本地计算上下文中启用此功能，只需将表示本地环境的以下代码行添加到 *.compute* 文件（通常命名为 *local.compute*）。
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

path ~/.azureml/share 是默认的基本文件夹路径。 可将它更改为可供脚本运行访问的任意本地绝对路径。 试验帐户名称、工作区名称和项目名称自动追加到基本目录名称，构成共享目录的完整路径。 例如，如果使用前面的默认值，则可以将文件写入到以下路径（并从中检索文件）：

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>配置 Docker 计算上下文（本地或远程）
若要在 Docker 计算上下文中启用此功能，必须将以下两行添加到本地或远程 Docker *.compute* 文件。

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>使用 `AZUREML_NATIVE_SHARE_DIRECTORY` 环境变量访问共享文件夹时，必须将 **sharedVolumes** 属性设置为 *true*。 否则，执行将会失败。

Docker 容器中运行的代码始终将此共享文件夹看作 */azureml-share/*。 Docker 容器看到的文件夹路径不可配置。 不要在代码中使用此文件夹名称。 而应始终使用环境变量名称 `AZUREML_NATIVE_SHARE_DIRECTORY` 来引用此文件夹。 此名称已映射到 Docker 主机或计算上下文中的本地文件夹。 此本地文件夹的基本目录是 *.compute* 文件中 `nativeSharedDirectory` 设置的可配置值。 主机上共享文件夹的本地路径（如果使用默认值）如下：
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>不管是在本地计算上下文还是本地 Docker 计算上下文中，本地磁盘上共享文件夹的路径都是相同的。 这意味着甚至可以在本地运行和本地 Docker 运行之间共享文件。

可以直接将输入数据放在这些文件夹中，计算机上的本地运行或 Docker 运行会选取它们。 也可以从本地运行或 Docker 运行将文件写入此文件夹，文件将会持久保存在该文件夹中，一直到执行生命周期结束。

有关详细信息，请参阅 [Azure Machine Learning Workbench 执行配置文件](experimentation-service-configuration-reference.md)。

>[!NOTE]
>HDInsight 计算上下文不支持 `AZUREML_NATIVE_SHARE_DIRECTORY` 环境变量。 但是，通过显式使用 Azure Blob 存储绝对路径在附加的 Blob 存储中进行读取和写入，可轻松实现相同的目的。

## <a name="option-3-use-external-durable-storage"></a>选项 3：使用外部持久存储

在执行期间，可以使用外部持久存储来持久保存状态。 此选项在以下情况下非常有用：
- 输入数据已存储在可从目标计算环境访问的持久存储中。
- 文件无需成为运行历史记录的一部分。
- 文件必须由不同计算环境中的执行共享。
- 文件的幸存时间必须比计算上下文本身更长。

从 Python/PySpark 代码使用 Azure Blob 存储就是一个例子。 下面是一个简短示例：

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

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

下面是将任意 Azure Blob 存储附加到 HDI Spark 运行时的简短示例：
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>结束语
由于 Azure 机器学习通过将整个项目文件夹复制到目标计算上下文来执行脚本，因此对于大型输入、输出和中间文件请格外小心。 对于大型文件事务，可以使用特殊的 outputs 文件夹、可通过 `AZUREML_NATIVE_SHARE_DIRECTORY` 环境变量访问的共享文件夹，或外部持久存储。 

## <a name="next-steps"></a>后续步骤
- 查看 [Azure Machine Learning Workbench 执行配置文件](experimentation-service-configuration-reference.md)一文。
- 了解[鸢尾花分类](tutorial-classifying-iris-part-1.md)教程中的项目如何使用 outputs 文件夹持久保存已训练的模型。
