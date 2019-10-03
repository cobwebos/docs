---
title: 探索 Windows 上的数据和模型
titleSuffix: Azure Data Science Virtual Machine
description: 在 Windows Data Science Virtual Machine 上执行数据探索和建模任务。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6af9f2c80915745f995144c24ae1649fb02a31ae
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192272"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Windows 数据科学虚拟机的十大功能

Windows Data Science Virtual Machine (DSVM) 是一个功能强大的数据科研开发环境, 您可以在其中执行数据浏览和建模任务。 环境已经建立并与多个常用的数据分析工具捆绑在一起, 使你可以轻松开始分析本地、云或混合部署。 

DSVM 与 Azure 服务密切合作。 它可以读取和处理已存储在 Azure、Azure SQL 数据仓库、Azure Data Lake、Azure 存储或 Azure Cosmos DB 中的数据。 它还可以利用其他分析工具, 例如 Azure 机器学习和 Azure 数据工厂。

本文介绍如何使用 DSVM 执行数据科学任务并与其他 Azure 服务进行交互。 可以在 DSVM 上执行的操作如下所示：

- 使用 Microsoft Machine Learning Server 和 Python 浏览数据并在 DSVM 上本地开发模型。
- 通过使用 Python 2、Python 3 和 Microsoft R, 在浏览器中使用 Jupyter 笔记本试验你的数据。(Microsoft R 是设计为性能的适用于企业的企业版。)
- 在 Azure 机器学习上部署通过 R 和 Python 生成的模型, 以便客户端应用程序可以使用简单的 web 服务接口访问你的模型。
- 使用 Azure 门户或 PowerShell 管理 Azure 资源。
- 通过在 DSVM 上创建 Azure 文件共享作为可装载驱动器, 扩展存储空间并跨整个团队共享大型数据集/代码。
- 使用 GitHub 与团队共享代码。 使用预安装的 Git 客户端访问存储库:Git Bash 和 Git GUI。
- 访问 azure 数据和分析服务, 如 Azure Blob 存储、Azure Data Lake、Azure HDInsight (Hadoop)、Azure Cosmos DB、Azure SQL 数据仓库和 Azure SQL 数据库。
- 使用预先安装在 DSVM 上的 Power BI Desktop 实例构建报表和仪表板, 并将它们部署到云中。
- 动态缩放你的 DSVM, 以满足你项目的需求。
- 在虚拟机上安装其他工具。   

> [!NOTE]
> 本文中列出的许多数据存储和分析服务都适用额外的使用费用。 有关详细信息, 请参阅[Azure 定价](https://azure.microsoft.com/pricing/)页。
> 
> 

## <a name="prerequisites"></a>先决条件

* 需要一个 Azure 订阅。 可以[注册免费试用版](https://azure.microsoft.com/free/)。
* [创建虚拟机](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)中提供了有关在 Azure 门户上设置 Data Science Virtual Machine 的说明。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>利用 Microsoft Machine Learning Server 浏览数据和开发模型
使用 R 和 Python 之类的语言即可在 DSVM 上执行数据分析。

对于 R, 可以使用 RStudio 之类的 IDE, 可以在 "开始" 菜单或桌面上找到该 IDE。 或者, 您可以使用针对 Visual Studio 的 R 工具。 Microsoft 在开源 CRAN R 的基础上提供了其他库, 以启用可伸缩分析, 并能够分析大于并行分块分析所允许的内存大小的数据。 

对于 Python，可以使用已预安装针对 Visual Studio 的 Python 工具 (PTVS) 扩展的 Visual Studio Community Edition 之类的 IDE。 默认情况下, 仅在 PTVS 上配置 Python 3.6 (根 Conda 环境)。 若要启用 Anaconda Python 2.7, 请执行以下步骤:

1. 转到 "**工具** > " "**python 工具** > " "**python 环境**", 然后选择 "在 Visual Studio 社区版中**自定义**", 为每个版本创建自定义环境。
1. 提供说明并将环境前缀路径设置为 Anaconda Python 2.7 的**c:\anaconda\envs\python2 (适用**。
1. 选择 "**自动检测** > **应用**" 以保存环境。

请参阅[PTVS 文档](https://aka.ms/ptvsdocs), 了解有关如何创建 Python 环境的更多详细信息。

现在已设置为创建新的 Python 项目。 中转到 > "**新建** > 项目" > "**python** ", 然后选择要生成的 Python 应用程序的类型。 可以通过右键单击 " **python 环境**", 然后选择 "**添加/删除 Python 环境**", 将当前项目的 Python 环境设置为所需版本 (python 2.7 或 3.6)。 可以在[产品文档](https://aka.ms/ptvsdocs)中找到有关使用 PTVS 的详细信息。

## <a name="use-jupyter-notebooks"></a>使用 Jupyter Notebook
Jupyter Notebook 提供了一个基于浏览器的 IDE, 用于数据浏览和建模。 你可以在 Jupyter 笔记本中使用 Python 2、Python 3 或 R (开放源代码和 Microsoft R Server)。

若要开始 Jupyter Notebook, 请选择 "**开始**" 菜单或桌面上的**Jupyter Notebook**图标。 在 DSVM 命令提示符下, 你还可以从具有现有```jupyter notebook```笔记本的目录或要在其中创建新笔记本的目录运行该命令。  

启动 Jupyter 后, 应该会看到一个目录, 其中包含一些预打包到 DSVM 的示例笔记本。 现在可以：

* 选择笔记本以查看代码。
* 通过选择 Shift + Enter 运行每个单元格。
* 选择 "**单元** > **运行**" 以运行整个笔记本。
* 选择 Jupyter 图标 (左上角), 然后选择右侧的 "**新建**" 按钮, 然后选择笔记本语言 (也称为内核) 来创建新笔记本。   

> [!NOTE]
> 目前支持 Jupyter 中的 Python 2.7、Python 3.6、R、Julia 和 PySpark 内核。 R 内核支持在开源 R 和 Microsoft R 中进行编程。   
> 
> 

当你在笔记本中时, 可以浏览你的数据, 生成模型, 并使用你选择的库来测试模型。

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>使用 Azure 机器学习训练和部署模型
构建并验证了模型后, 下一步通常是将它部署到生产环境中。 此步骤使客户端应用程序可以实时或批处理模式调用模型预测。 Azure 机器学习提供一种机制，可用于操作使用 R 或 Python 生成的模型。

在 Azure 机器学习中操作模型时, 会公开 web 服务。 它允许客户端进行 REST 调用以传入输入参数, 并以输出的形式接收模型中的预测。   

> [!NOTE]
> 如果尚未注册 Azure 机器学习, 可以通过访问[Azure 机器学习 Studio](https://studio.azureml.net/)主页并选择 "**开始**使用" 来获取免费工作区或标准工作区。   
> 
> 

### <a name="build-and-operationalize-python-models"></a>构建和操作 Python 模型
下面是在 Python Jupyter 笔记本中开发的代码片段, 它使用 Scikit-learn 库生成简单模型:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

用于将 Python 模型部署到的方法 Azure 机器学习将模型的预测封装到函数中, 并使用预安装的 Azure 机器学习 Python 库提供的属性对其进行修饰。 属性表示 Azure 机器学习工作区 ID、API 密钥以及输入和返回参数。  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

客户端现在可以调用 Web 服务。 便利性包装构造 REST API 请求。 下面是使用 web 服务的示例代码:

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> 目前, 仅在 Python 2.7 上支持 Azure 机器学习库。   
> 
> 

### <a name="build-and-operationalize-r-models"></a>生成和操作 R 模型
你可以将基于 Data Science Virtual Machine 或其他位置构建的 R 模型部署到 Azure 机器学习, 其方式类似于对 Python 执行的操作。 下面是相关步骤：

1. 创建一个 json 文件来提供工作区 ID 和身份验证令牌。 
2. 编写模型的预测函数的包装。
3. 在```publishWebService``` Azure 机器学习库中调用以传入函数包装。  

使用以下过程和代码片段, 在 Azure 机器学习中将模型设置、构建、发布和使用为 web 服务。

#### <a name="set-up"></a>设置

在主目录下名```.azureml```为的目录下创建一个 json 文件。 输入 Azure 机器学习工作区中的参数。

下面是设置的 json 文件结构:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>使用 R 生成模型，并在 Azure 机器学习中发布它

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>使用部署在 Azure 机器学习中的模型
若要从客户端应用程序使用该模型, 请使用 Azure 机器学习库按名称查找已发布的 web services。 `services`使用 API 调用来确定终结点。 然后只需调用 `consume` 函数，并传入要预测的数据帧。

使用以下代码来使用发布为 Azure 机器学习 web 服务的模型:

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

请参阅[机器学习 Studio 中的 R 包的](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning)详细信息。

## <a name="manage-azure-resources"></a>管理 Azure 资源
DSVM 不允许在虚拟机上本地生成分析解决方案。 它还允许您访问 Azure 云平台上的服务。 Azure 提供多个计算、存储、数据分析和其他服务, 你可以从 DSVM 进行管理和访问。

若要管理 Azure 订阅和云资源, 有两个选项:
+ 使用浏览器并中转到[Azure 门户](https://portal.azure.com)。

+ 使用 PowerShell 脚本。 从桌面上的快捷方式或从 "**开始**" 菜单运行 Azure PowerShell。 有关完整详细信息, 请参阅[Microsoft Azure PowerShell 文档](../../powershell-azure-resource-manager.md)。 

## <a name="extend-storage-by-using-shared-file-systems"></a>使用共享文件系统扩展存储
数据科学家可以在团队内共享大型数据集、代码或其他资源。 DSVM 的可用空间大约为 45 GB。 若要扩展存储, 可以使用 Azure 文件, 并将其装载到一个或多个 DSVM 实例上或通过 REST API 访问。 你还可以使用[Azure 门户](../../virtual-machines/windows/attach-managed-disk-portal.md)或使用[Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md)添加额外的专用数据磁盘。 

> [!NOTE]
> Azure 文件共享上的最大空间为 5 TB。 每个文件的大小限制为 1 TB。 

可以在 Azure PowerShell 中使用此脚本来创建 Azure 文件共享:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

现在, 你已创建 Azure 文件共享, 可以将它装载到 Azure 中的任何虚拟机上。 建议将 VM 放入存储帐户所在的同一个 Azure 数据中心, 以避免延迟和数据传输费用。 下面是在 DSVM 上装载驱动器的 Azure PowerShell 命令:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

现在可以像访问 VM 上的任何正常驱动器一样访问此驱动器。

## <a name="share-code-in-github"></a>在 GitHub 中共享代码
GitHub 是一个代码存储库, 可以通过使用开发人员社区共享的技术来查找各种工具的代码示例和源代码。 它使用 Git 作为跟踪和存储代码文件版本的技术。 GitHub 也是一个平台, 可以在其中创建自己的存储库来存储团队的共享代码和文档、实现版本控制, 并控制谁有权查看和贡献代码。 

有关使用 Git 的详细信息，请访问 [GitHub 帮助页](https://help.github.com/)。 可以将 GitHub 用作与团队协作、使用社区开发的代码和将代码提交给社区的方法之一。

DSVM 随客户端工具一起加载到命令行和 GUI 上, 以访问 GitHub 存储库。 适用于 Git 和 GitHub 的命令行工具称为 Git Bash。 Visual Studio 安装在 DSVM 上, 并且具有 Git 扩展。 可以在 "**开始**" 菜单和桌面上查找这些工具的图标。

若要从 GitHub 存储库下载代码，请使用 ```git clone``` 命令。 例如, 要将 Microsoft 发布的数据科研存储库下载到当前目录, 可以在 Git Bash 中运行以下命令:

    git clone https://github.com/Azure/DataScienceVM.git

在 Visual Studio 中，可以执行相同的克隆操作。 以下屏幕截图演示了如何在 Visual Studio 中访问 Git 和 GitHub 工具:

![Visual Studio 的屏幕截图，其中显示了 GitHub 连接](./media/vm-do-ten-things/VSGit.PNG)

可以从 github.com 上提供的资源中找到有关使用 Git 处理 GitHub 存储库的详细信息。 [备忘单](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf)是一个有用的参考。

## <a name="access-azure-data-and-analytics-services"></a>访问 Azure 数据和分析服务
### <a name="azure-blob-storage"></a>Azure Blob 存储
Azure Blob 存储是一种可靠、经济的云存储服务, 适用于大数据和小数据。 本部分介绍如何将数据移动到 Blob 存储, 以及如何访问存储在 Azure blob 中的数据。

#### <a name="prerequisites"></a>先决条件

* 从[Azure 门户](https://portal.azure.com)创建 Azure Blob 存储帐户。

   ![Azure 门户中的存储帐户创建过程的屏幕截图](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* 确认已预安装命令行 AzCopy 工具: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```。 包含 azcopy 的目录已存在于路径环境变量上, 因此, 在运行此工具时可以避免键入完整的命令路径。 有关 AzCopy 工具的详细信息, 请参阅[AzCopy 文档](../../storage/common/storage-use-azcopy.md)。
* 启动 Azure 存储资源管理器工具。 你可以从[存储资源管理器网页](https://storageexplorer.com/)中下载它。 

   ![Azure 存储资源管理器访问存储帐户的屏幕截图](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>将数据从 VM 移动到 Azure blob:AzCopy

若要在本地文件和 Blob 存储之间移动数据, 可以在命令行或 PowerShell 中使用 AzCopy:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

将**C:\myfolder**替换为文件的存储路径, 将**Mystorageaccount**替换为 blob 存储帐户名称, 使用容器名称**mycontainer** , 将**存储帐户密钥**替换为 blob 存储访问密钥。 可以在[Azure 门户](https://portal.azure.com)中找到存储帐户凭据。

在 PowerShell 或命令提示符下运行 AzCopy 命令。 下面是 AzCopy 命令的一些示例用法:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

运行 AzCopy 命令以复制到 Azure blob 后, 文件将显示在 Azure 存储资源管理器中。

![存储帐户的屏幕截图, 显示上传的 CSV 文件](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>将数据从 VM 移动到 Azure blob:Azure 存储资源管理器

还可以通过使用 Azure 存储资源管理器, 从 VM 中的本地文件上传数据:

* 若要将数据上传到容器, 请选择目标容器, 然后选择 "**上传**" 按钮。![Azure 存储资源管理器中的 "上传" 按钮的屏幕截图](./media/vm-do-ten-things/storage-accounts.png)
* 选择 "**文件**" 框右侧的省略号 ( **...** ), 选择要从文件系统上传的一个或多个文件, 然后选择 "**上载**" 开始上载文件。!["上载文件" 对话框的屏幕截图](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>从 Azure blob 读取数据:机器学习读取器模块

在 Azure 机器学习 Studio 中, 可以使用 "导入数据" 模块从 blob 读取数据。

![机器学习工作室中的导入数据模块的屏幕截图](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>从 Azure blob 读取数据:Python ODBC

可以使用 BlobService 库直接从 Jupyter 笔记本中的 blob 或 Python 程序中读取数据。

首先, 导入所需的包:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

然后, 插入 Blob 存储帐户凭据, 并从 Blob 读取数据:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

数据作为数据帧读取:

![前 10 行数据的屏幕截图](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage 是适用于大数据分析工作负荷的超大规模存储库, 并且与 Hadoop 分布式文件系统 (HDFS) 兼容。 它适用于 Hadoop、Spark 和 Azure Data Lake Analytics。 在本部分中, 你将了解如何使用 Azure Data Lake Analytics 将数据移动到 Azure Data Lake Storage 并运行分析。

#### <a name="prerequisites"></a>先决条件

* 在[Azure 门户](https://portal.azure.com)中创建 Azure Data Lake Analytics 实例。

   ![使用 Azure 门户创建 Data Lake Analytics 实例的屏幕截图](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* [Visual studio 插件的 Azure Data Lake 和流分析工具](https://www.microsoft.com/download/details.aspx?id=49504)已在虚拟机上的 Visual Studio 社区版中安装。 启动 Visual Studio 并登录 Azure 订阅后, 你应该会在 Visual Studio 的左侧看到 Azure Data Analytics 帐户和存储。

   ![Visual Studio 中 Data Lake 工具的插件屏幕截图](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>将数据从 VM 移动到 Data Lake:Azure Data Lake 资源管理器

你可以使用 Azure Data Lake 资源管理器将[虚拟机上的本地文件中的数据上传到 Data Lake Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)。

你还可以使用[Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)构建数据管道, 以操作将数据移动到 Azure Data Lake 或从移动。 [本文](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/)将指导您完成生成数据管道的步骤。

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>将数据从 Azure blob 读取到 Data Lake:U-SQL

如果数据驻留在 Azure Blob 存储中, 则可以直接在 SQL 查询中从 Azure blob 读取数据。 编写 U SQL 查询之前, 请确保 Blob 存储帐户已链接到 Azure Data Lake 实例。 请参阅 Azure 门户, 查找 Azure Data Lake Analytics 仪表板, 选择 "**添加数据源**", 选择**azure 存储**的存储类型, 并插入 azure 存储帐户名称和密钥。 然后, 可以引用存储帐户中存储的数据。

!["添加数据源" 对话框的屏幕截图](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

在 Visual Studio 中, 可以从 Blob 存储读取数据、操作数据和工程功能, 并将结果数据发送到 Azure Data Lake 或 Azure Blob 存储。 引用 Blob 存储中的数据时, 请使用**wasb://** 。 引用 Azure Data Lake 中的数据时, 请使用**swbhdfs://** 。

你可以在 Visual Studio 中使用以下 SQL 查询:

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

将查询提交到服务器后, 将显示作业的状态。

![作业状态图的屏幕截图](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>查询数据 Data Lake:U-SQL

Azure Data Lake 中的数据集之后, 可以使用引入[语言](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md)来查询和浏览数据。 U SQL 语言与 T-sql 类似, 但合并了中的一些功能, 以便C#用户可以编写自定义的模块和用户定义的函数。 可以使用上一步骤中的脚本。

将查询提交到服务器后, tripdata_summary。CSV 出现在 Azure Data Lake 资源管理器中。 您可以通过右键单击该文件来预览数据。

![Data Lake 资源管理器中的 CSV 文件的屏幕截图](./media/vm-do-ten-things/USQL_create_summary.png)

此时将显示文件信息:

![文件摘要信息的屏幕截图](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop 群集
Azure HDInsight 是云中托管的 Apache Hadoop、Spark、HBase 和风暴服务。 可以通过 Data Science Virtual Machine 轻松使用 Azure HDInsight 群集。

#### <a name="prerequisites"></a>先决条件

* 从[Azure 门户](https://portal.azure.com)创建 Azure Blob 存储帐户。 此存储帐户用于存储 HDInsight 群集的数据。

   ![从 Azure 门户创建存储帐户的屏幕截图](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* 自定义[Azure 门户](../team-data-science-process/customize-hadoop-cluster.md)中的 Azure HDInsight Hadoop 分类。
  
   在创建 HDInsight 群集时将其与创建的存储帐户相链接。 此存储帐户用于访问可在群集中处理的数据。

   ![用于链接使用 HDInsight 群集创建的存储帐户的选择](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* 创建群集后, 启用对其头节点的远程桌面访问。 请记住你在此处指定的远程访问凭据, 因为你将在后续步骤中需要它们。

   ![用于启用对 HDInsight 群集的远程访问的 "远程桌面" 按钮](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* 创建 Azure 机器学习工作区。 机器学习试验存储在此机器学习工作区中。 选择门户中突出显示的选项, 如以下屏幕截图所示:

   ![创建 Azure 机器学习工作区](./media/vm-do-ten-things/Create_ML_Space.PNG)

* 输入工作区的参数。

   ![输入机器学习工作区参数](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* 使用 IPython 笔记本上传数据。 导入所需的包, 插入凭据, 在存储帐户中创建数据库, 然后将数据加载到 HDI 群集。

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

或者, 可以按照[本演练](../team-data-science-process/hive-walkthrough.md)将 NYC 出租车数据上传到 HDI 群集。 主要步骤如下所示：
  
* 使用 AzCopy 将 zipped Csv 从公共 blob 下载到本地文件夹。
* 使用 AzCopy 将解压缩的 Csv 从本地文件夹上传到 HDI 群集。
* 登录到 Hadoop 群集的头节点, 为探索数据分析做准备。

将数据加载到 HDI 群集后, 可以在 Azure 存储资源管理器中检查数据。 在 HDI 群集中创建了 nyctaxidb 数据库。

#### <a name="data-exploration-hive-queries-in-python"></a>数据浏览:Python 中的 Hive 查询

由于数据在 Hadoop 群集中, 因此你可以使用 pyodbc 包来连接到 Hadoop 群集, 并通过使用 Hive 执行探索和特征工程来查询数据库。 您可以查看在必备步骤中创建的现有表。

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![查看现有表](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

请查看每月的记录数以及行程表中已付小费或未付小费的频率：

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![每个月的记录数的走势图](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![行程频率图](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

你还可以计算装货位置与下拉位置之间的距离, 然后将其与行程距离进行比较。

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![拾取和下拉列表的最前面几行](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![拾取/拖离行程距离的绘图](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

现在, 让我们准备一组用于建模的 downsampled (1%) 的数据。 可以在机器学习读取器模块中使用此数据。

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

现在, 将联接的内容插入到前面的内部表中。

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

一段时间后, 可以看到已在 Hadoop 群集中加载数据:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![表中前几行的数据](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

#### <a name="read-data-from-hdi-by-using-machine-learning-reader-module"></a>使用机器学习读取 HDI 的数据: 读取器模块

你还可以使用机器学习 Studio 中的 "读取器" 模块来访问 Hadoop 群集中的数据库。 插入 HDI 群集和 Azure 存储帐户的凭据, 以便能够使用 HDI 群集中的数据库构建机器学习模型。

![读取器模块属性](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

然后, 您可以查看已评分的数据集:

![查看已评分数据集](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL 数据仓库和数据库
Azure SQL 数据仓库是一项弹性数据仓库即服务, 具有企业级 SQL Server 体验。

可以按照[本文](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)中的说明来预配 Azure SQL 数据仓库。 预配 SQL 数据仓库后, 可以使用[此演练](../team-data-science-process/sqldw-walkthrough.md), 通过使用 SQL 数据仓库中的数据执行数据上传、探索和建模。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB 是云中的 NoSQL 数据库。 你可以使用它来处理 JSON 格式的文档, 并使用来存储和查询文档。

使用以下必备步骤来访问 DSVM 中的 Azure Cosmos DB:

1. DSVM 上已安装 Azure Cosmos DB Python SDK。 若要对其进行```pip install pydocumentdb --upgrade```更新, 请从命令提示符处运行。
2. 从[Azure 门户](https://portal.azure.com)创建 Azure Cosmos DB 帐户和数据库。
3. 从[Microsoft 下载中心](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d)下载 Azure Cosmos DB 数据迁移工具并提取到所选的目录中。
4. 使用迁移工具的以下命令参数将存储在[公共 blob](https://cahandson.blob.core.windows.net/samples/volcano.json)中的 JSON 数据 (火山数据) 导入到 Azure Cosmos DB 中。 (使用安装 Azure Cosmos DB 数据迁移工具的目录中的 dtui.exe。)通过以下参数输入源和目标位置：
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

导入数据后, 可以前往 Jupyter 并打开标题为*DocumentDBSample*的笔记本。 它包含用于访问 Azure Cosmos DB 和执行一些基本查询的 Python 代码。 可以通过访问服务的[文档页](https://docs.microsoft.com/azure/cosmos-db/)来了解有关 Azure Cosmos DB 的详细信息。

## <a name="use-power-bi-reports-and-dashboards"></a>使用 Power BI 报表和仪表板 
您可以在 Power BI Desktop 中直观显示火山 JSON Azure Cosmos DB 文件, 以获取对数据的可视化见解。 [Power BI 文章](../../cosmos-db/powerbi-visualize.md)中提供了详细步骤。 下面是概要步骤：

1. 打开 Power BI Desktop 并选择“获取数据”。 将 URL 指定为: https://cahandson.blob.core.windows.net/samples/volcano.json 。
2. 应会看到作为列表导入的 JSON 记录。 将列表转换为表, 以便 Power BI 可以使用它。
4. 通过选择展开 (箭头) 图标来展开列。
5. 请注意, 该位置是**记录**字段。 展开记录并仅选择坐标。 **坐标**是列表列。
6. 添加一个新列, 将列表坐标列转换为以逗号分隔的**经纬度**列。 通过使用公式```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```来连接坐标列表字段中的两个元素。
7. 将**提升**列转换为 decimal, 然后选择 "**关闭**" 和 "**应用**" 按钮。

你可以粘贴以下代码, 而不是执行前面的步骤。 它编写了 Power BI 的高级编辑器中使用的步骤, 以查询语言编写数据转换。

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

现在，Power BI 数据模型中有数据。 Power BI Desktop 实例应显示如下:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

您可以使用数据模型开始生成报表和可视化效果。 可以按照[此 Power BI 文章](../../cosmos-db/powerbi-visualize.md#build-the-reports)中的步骤来生成报表。

## <a name="scale-the-dsvm-dynamically"></a>动态缩放 DSVM 
可以增加和减少 DSVM, 以满足项目的需求。 如果不需要在晚上或周末使用 VM, 可以从[Azure 门户](https://portal.azure.com)关闭 vm。

> [!NOTE]
> 如果在 VM 上只使用操作系统的 "关闭" 按钮, 则会产生计算费用。  
> 
> 

你可能需要处理一些大规模分析并且需要更多的 CPU、内存或磁盘容量。 如果是这样, 则可以在 CPU 核心、基于 GPU 的实例 (用于深度学习、内存容量和磁盘类型 (包括固态驱动器)) 中找到所选的 VM 大小, 以满足计算和预算需求。 [Azure 虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)页上提供了 vm 的完整列表及其每小时计算定价。

同样, 你对 VM 处理容量的需求可能会降低。 (例如: 已将主要工作负荷移动到 Hadoop 或 Spark 群集。)然后, 可以从[Azure 门户](https://portal.azure.com)中缩减群集, 并中转到 VM 实例的设置。 

## <a name="add-more-tools"></a>添加更多工具
内置于 DSVM 中的工具可以解决许多常见的数据分析需求。 这样可以节省时间, 因为你无需逐个安装和配置环境。 它还会为你节省资金, 因为你只需为你使用的资源付费。

可以使用本文中所述的其他 Azure 数据和分析服务来增强分析环境。 在某些情况下, 可能需要额外的工具, 包括一些专用的合作伙伴工具。 你对虚拟机拥有完全管理访问权限, 可以安装所需的新工具。 还可以安装未预安装的 Python 和 R 中的其他程序包。 对于 Python, 可以使用```conda```或。 ```pip``` 对于 r, 可以在 r ```install.packages()```控制台中使用, 或者使用 IDE 并选择 "**包** > " "**安装包**"。

## <a name="deep-learning"></a>深度学习

除了基于框架的示例外, 还可以获取一组已在 DSVM 上验证的综合演练。 这些演练可帮助你快速开始开发域中的深度学习应用程序, 如图像和文本/语言理解。   


- [在不同的框架中运行神经网络](https://github.com/ilkarman/DeepLearningFrameworks)：此演练演示如何将代码从一个框架迁移到另一个框架。 它还演示如何在框架之间比较模型和运行时性能。 

- [生成端到端解决方案以检测图像中的产品的操作指南](https://github.com/Azure/cortana-intelligence-product-detection-from-images)：图像检测是一种能够对图像中的对象进行定位和分类的技术。 这项技术可能会在许多现实业务领域中带来巨大的回报。 例如，零售商可以使用此技术确定客户已从货架上选取哪个产品。 从而，此信息可帮助商店管理产品库存。 

- [深入了解音频](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/):本教程介绍如何为 "[市内声音" 数据集](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)的 "音频事件检测" 训练深度学习模型。 它还提供了有关如何使用音频数据的概述。

- [文本文档分类](https://github.com/anargyri/lstm_han)：本演练演示如何构建和训练两个神经网络体系结构:分层注意网络和长短期记忆 (LSTM) 网络。 这些神经网络使用用于深度学习的 Keras API 对文本文档进行分类。 Keras 是最热门的三个最受欢迎的深度学习框架的前端:Microsoft Cognitive Toolkit、TensorFlow 和 Theano。

## <a name="summary"></a>总结
本文介绍了可在 Microsoft Data Science Virtual Machine 上执行的某些操作。 你还可以执行更多操作, 使 DSVM 成为有效的分析环境。

