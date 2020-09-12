---
title: 在 Windows 上探索数据和模型
titleSuffix: Azure Data Science Virtual Machine
description: 在 Windows Data Science Virtual Machine 上执行数据探索和建模任务。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: b570968a66a0cfd60ac4d6ce6dd7dc31a1003240
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440450"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>使用 Windows Data Science Virtual Machine 的数据科学

Windows Data Science Virtual Machine (DSVM) 是一个功能强大的数据科学开发环境，可在其中执行数据探索和建模任务。 该环境本身内置和捆绑了多款热门数据分析工具，便于针对本地、云或混合部署开始分析。 

DSVM 与 Azure 服务紧密协同工作。 它可以读取和处理已存储在 Azure 中的数据、Azure Synapse (以前的 SQL DW) 、Azure Data Lake、Azure 存储或 Azure Cosmos DB。 它还可以利用其他分析工具，如 Azure 机器学习。

本文介绍如何使用 DSVM 执行数据科学任务，以及如何与其他 Azure 服务交互。 可以在 DSVM 上执行的操作如下所示：

- 通过使用 Python 2、Python 3 和 Microsoft R，在浏览器中使用 Jupyter 笔记本对数据进行试验。（Microsoft R 是适用于企业的 R 版本，进行了性能优化设计。）
- 使用 Microsoft Machine Learning Server 和 Python 在 DSVM 上本地探索数据和开发模型。
- 使用 Azure 门户或 PowerShell 管理 Azure 资源。
- 通过将 Azure 文件存储共享创建为可在 DSVM 上装载的驱动器，即可扩展存储空间并跨整个团队共享大型数据集/代码。
- 使用 GitHub 与团队共享代码。 使用下列预安装的 Git 客户端访问存储库：Git Bash 和 Git GUI。
- 访问 azure 数据和分析服务，如 Azure Blob 存储、Azure Cosmos DB、Azure Synapse (以前的 SQL DW) 和 Azure SQL 数据库。
- 使用 DSVM 上预安装的 Power BI Desktop 实例生成报表和仪表板，然后将它们部署到云中。

- 在虚拟机上安装其他工具。   

> [!NOTE]
> 本文中列出的许多数据存储和分析服务将收取额外的使用费用。 有关详细信息，请参阅 [Azure 定价](https://azure.microsoft.com/pricing/)页。
> 
> 

## <a name="prerequisites"></a>先决条件

* 需要一个 Azure 订阅。 可以[注册免费试用版](https://azure.microsoft.com/free/)。
* 有关在 Azure 门户上配置 Data Science Virtual Machine 的说明，请参阅[创建虚拟机](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>使用 Jupyter Notebook
Jupyter Notebook 提供基于浏览器的 IDE，用于数据探索和建模。 可以在 Jupyter 笔记本中使用 Python 2、Python 3 或 R（开源和 Microsoft R Server）。

若要启动 Jupyter Notebook，请在“开始”菜单或桌面上选择“Jupyter Notebook”图标。  在 DSVM 命令提示符处，还可以从包含现有笔记本或想在其中创建新笔记本的目录中运行 ```jupyter notebook``` 命令。  

启动 Jupyter 后，导航到 `/notebooks` 目录，以获取预打包到 DSVM 中的多个示例笔记本。 现在可以：

* 单击笔记本以查看代码。
* 选择 Shift+Enter 以运行每个单元格。
* 选择“单元” > “运行”，运行整个笔记本。 
* 依次选择 Jupyter 图标（左上角），右侧的“新建”按钮和笔记本语言（也称为内核），创建新的笔记本。   

> [!NOTE]
> Jupyter 当前支持 Python 2.7、Python 3.6、R、Julia 和 PySpark 内核。 R 内核支持以开放源代码 R 和 Microsoft R 进行编程。   
> 
> 

在笔记本中，可探索数据、生成模型、使用自己选择的库测试模型。

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>使用 Microsoft Machine Learning Server 探索数据和开发模型
使用 R 和 Python 之类的语言即可在 DSVM 上执行数据分析。

对于 R，可以使用 RStudio 等 IDE（可在“开始”菜单或桌面找到）。 也可使用针对 Visual Studio 的 R 工具。 Microsoft 额外提供了基于开源/CRAN-R 的库，以便支持可扩展的分析，并提供分析大型数据（大于并行区块分析所允许的内存大小）的能力。 

对于 Python，可以使用已预安装针对 Visual Studio 的 Python 工具 (PTVS) 扩展的 Visual Studio Community Edition 之类的 IDE。 默认情况下，PTVS 上仅配置了根 Conda 环境 Python 3.6。 若要启用 Anaconda Python 2.7，请按以下步骤操作：

1. 在 Visual Studio Community Edition 中，转到“工具” > “Python 工具” > “Python 环境”，并选择“+ 自定义”，为每个版本创建自定义环境。
1. 提供描述并将环境前缀路径设置为 c:\anaconda\envs\python2（适用于 Anaconda Python 2.7）。
1. 选择“自动检测” > “应用”以保存环境。

有关如何创建 Python 环境的详细信息，请参阅 [PTVS 文档](https://aka.ms/ptvsdocs)。

现在即可开始创建新的 Python 项目。 转到“文件” > “新建” > “项目” > “Python”，并选择要生成的 Python 应用程序的类型。 可以将当前项目的 Python 环境设置为所需版本（Python 2.7 或 3.6），方法是右键单击“Python 环境”，然后选择“添加/删除 Python 环境”。 要详细了解如何使用 PTVS，请参阅[产品文档](https://aka.ms/ptvsdocs)。



## <a name="manage-azure-resources"></a>管理 Azure 资源
DSVM 不仅允许在虚拟机上本地生成分析解决方案。 它还允许访问 Azure 云平台上的服务。 Azure 提供多个计算、存储、数据分析和其他服务，可以从 DSVM 管理并访问这些服务。

可选用两种方法管理 Azure 订阅和云资源：
+ 使用 Web 浏览器并转到 [Azure 门户](https://portal.azure.com)。

+ 使用 PowerShell 脚本。 从桌面快捷方式或“开始”菜单运行 Azure PowerShell。 请参阅 [Microsoft Azure PowerShell 文档](../../powershell-azure-resource-manager.md)以了解全部详细信息。 

## <a name="extend-storage-by-using-shared-file-systems"></a>使用共享文件系统扩展存储
数据科学家可以在团队内共享大型数据集、代码或其他资源。 DSVM 约有 45 GB 的可用空间。 要扩展存储，可以使用 Azure 文件存储，将它装载到一个或多个 DSVM 实例或通过 REST API 访问它。 还可以使用 [Azure 门户](../../virtual-machines/windows/attach-managed-disk-portal.md)或 [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) 添加其他专用数据磁盘。 

> [!NOTE]
> Azure 文件存储共享上的最大可用空间为 5 TB。 每个文件的大小限制为 1 TB。 

可以在 Azure PowerShell 中使用此脚本创建 Azure 文件存储共享。

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

现已创建 Azure 文件存储共享，可以将它装载到 Azure 中的任何虚拟机中。 建议将 VM 和存储帐户置于同一 Azure 数据中心，以避免延迟和产生数据传输费用。 可使用以下 Azure PowerShell 命令在 DSVM 上装载驱动器：

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

现在可以像访问 VM 上的任何正常驱动器一样访问此驱动器。

## <a name="share-code-in-github"></a>在 GitHub 中共享代码
GitHub 是一个代码存储库，可在其中找到代码示例和资源，用于通过开发人员社区共享的技术构建各种工具。 它使用 Git 作为跟踪和存储代码文件版本的技术。 GitHub 也是一个平台，可以在其中创建自己的存储库来存储团队的共享代码和文档、实现版本控制，还可以控制谁有权查看和贡献代码。 

有关使用 Git 的详细信息，请访问 [GitHub 帮助页](https://help.github.com/)。 可以将 GitHub 用作团队协作、利用社区开发的代码以及向社区回馈代码的一个途径。

DSVM 包含用于访问 GitHub 存储库的客户端工具，可通过命令行和 GUI 使用。 可作用于 Git 和 GitHub 的命令行工具称为 Git Bash。 DSVM 上安装了 Visual Studio 且有 Git 扩展。 可以在“开始”菜单和桌面上找到这些工具的图标。

若要从 GitHub 存储库下载代码，请使用 ```git clone``` 命令。 例如，要将 Microsoft 发布的数据科学存储库下载到当前目录，可在 Git Bash 中运行以下命令：

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

在 Visual Studio 中，可以执行相同的克隆操作。 以下屏幕截图演示了如何在 Visual Studio 中访问 Git 和 GitHub 工具：

![Visual Studio 的屏幕截图，其中显示了 GitHub 连接](./media/vm-do-ten-things/VSGit.png)

可以从 github.com 上提供的资源中找到有关使用 Git 操作 GitHub 存储库的更多信息。 [备忘单](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf)是一个有用的参考。

## <a name="access-azure-data-and-analytics-services"></a>访问 Azure 数据和分析服务
### <a name="azure-blob-storage"></a>Azure Blob 存储
Azure Blob 存储是适合大小数据的经济可靠云存储服务。 本部分介绍如何将数据移动到 Blob 存储以及如何访问 Azure Blob 中存储的数据。

#### <a name="prerequisites"></a>先决条件

* 从 [Azure 门户](https://portal.azure.com)创建 Azure Blob 存储帐户。

   ![Azure 门户中存储帐户创建流程的屏幕截图](./media/vm-do-ten-things/create-azure-blob.png)

* 确认已预安装命令行 AzCopy 工具：```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```。 包含 azcopy.exe 的目录已在 PATH 环境变量中，因此运行此工具时不用键入完整命令路径。 有关 AzCopy 工具的详细信息，请参阅 [AzCopy 文档](../../storage/common/storage-use-azcopy.md)。
* 启动 Azure 存储资源管理器工具。 可从[存储资源管理器网页](https://storageexplorer.com/)下载它。 

   ![Azure 存储资源管理器访问存储帐户时的屏幕截图](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>将数据从 VM 移动到 Azure blob：AzCopy

若要在本地文件和 Blob 存储之间移动数据，可以在命令行或 PowerShell 中使用 AzCopy：

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

将“C:\myfolder”替换为存储着文件的路径，将“mystorageaccount”替换为 Blob 存储帐户名称，将“mycontainer”替换为容器名称，将“storage account key”替换为 Blob 存储访问密钥。    可以在 [Azure 门户](https://portal.azure.com)中找到存储帐户凭据。

在 PowerShell 中或从命令提示符下运行 AzCopy 命令。 以下是 AzCopy 命令的一些使用示例：

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

运行 AzCopy 命令以复制到 Azure blob 后，文件会显示在 Azure 存储资源管理器中。

![存储帐户的屏幕截图，其中显示了上传的 CSV 文件](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>将数据从 VM 移动到 Azure blob：Azure 存储资源管理器

还可使用 Azure 存储资源管理器上传来自 VM 中本地文件的数据：

* 要将数据上传到容器，请选择目标容器，然后选择“上传”按钮。![Azure 存储资源管理器中上传按钮的屏幕截图](./media/vm-do-ten-things/storage-accounts.png)
* 选择“文件”框右侧的省略号 (…)，选择要从文件系统上传的一个或多个文件，然后选择“上传”开始上传文件。  ![“上传文件”对话框的屏幕截图](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>读取 Azure blob 中的数据：Python ODBC

在 Jupyter 笔记本或 Python 程序中，可以使用 BlobService 库直接读取 blob 中的数据。

首先，导入所需的包：

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

然后插入 Blob 存储帐户凭据，并读取 blob 中的数据：

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

以数据帧的形式读取数据：

![前 10 行数据的屏幕截图](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-formerly-sql-dw-and-databases"></a>Azure Synapse Analytics (以前的 SQL DW) 和数据库
Azure Synapse Analytics (以前的 SQL DW) 是一种具有企业级 SQL Server 体验的弹性数据仓库即服务。

可以按照 [本文](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)中的说明预配 Azure Synapse Analytics。 预配 Azure Synapse Analytics 后，可以使用 [此演练](../team-data-science-process/sqldw-walkthrough.md) ，通过 Azure Synapse 分析中的数据执行数据上传、探索和建模。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB 是云中的 NoSQL 数据库。 可用其处理 JSON 等文档及存储和查询文档。

使用以下先决步骤从 DSVM 访问 Azure Cosmos DB：

1. DSVM 上已安装 Azure Cosmos DB Python SDK。 若要更新它，请在命令提示符下运行 ```pip install pydocumentdb --upgrade```。
2. 从 [Azure 门户](https://portal.azure.com)创建 Azure Cosmos DB 帐户和数据库。
3. 从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53595)下载 Azure Cosmos DB 数据迁移工具，并提取到你选择的目录。
4. 在迁移工具中使用以下命令参数，将存储在 [公共 blob](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) 中的 JSON 数据（Volcano 数据）导入 Azure Cosmos DB。 （使用安装了 Azure Cosmos DB 数据迁移工具的目录中的 dtui.exe。）通过以下参数输入源和目标位置：
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

导入数据后，可转到 Jupyter 并打开名为“DocumentDBSample”的笔记本。 它包含用于访问 Azure Cosmos DB 和执行某些基本查询所需的 Python 代码。 访问 Azure Cosmos DB 的[文档页](https://docs.microsoft.com/azure/cosmos-db/)，可了解有关该服务的详细信息。

## <a name="use-power-bi-reports-and-dashboards"></a>使用 Power BI 报告和仪表板 
可在 Power BI Desktop 中对上一 Azure Cosmos DB 示例中的 Volcano JSON 文件进行可视化，以便获得有关该数据的直观见解。 [Power BI 文章](../../cosmos-db/powerbi-visualize.md)中提供了详细步骤。 下面是概要步骤：

1. 打开 Power BI Desktop 并选择“获取数据”。 将 URL 指定为 `https://cahandson.blob.core.windows.net/samples/volcano.json`。
2. 应会看到导入的 JSON 记录显示为一个列表。 将此列表转换为表，使 Power BI 可以处理它。
4. 选择展开（箭头）按钮来展开列。
5. 注意到位置是一个记录字段。 展开记录并仅选择坐标。 坐标是列表中的一列。
6. 添加新列，将列表的坐标列转换为逗号分隔的 LatLong 列。 使用公式 ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` 串接坐标列表字段中的两个元素。
7. 将“海拔”列转换为十进制，并选择“关闭”和“应用”按钮。  

可以不执行以上步骤，而是粘贴以下代码。 它将 Power BI 中“高级编辑器”中的步骤编写为脚本，以查询语言编写数据转换。

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

现在，Power BI 数据模型中有数据。 你的 Power BI Desktop 实例应如下所示：

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

可使用数据模型开始生成报告和可视化。 可按照[这篇 Power BI 文章](../../cosmos-db/powerbi-visualize.md#build-the-reports)中的步骤生成报告。

## <a name="scale-the-dsvm-dynamically"></a>动态缩放 DSVM 
可动态缩放 DSVM 以满足项目需求。 如果晚上或周末不需要使用 VM ，可从 [Azure 门户](https://portal.azure.com)关闭 VM。

> [!NOTE]
> 如果使用 VM 操作系统中的关机按钮，将产生计算费用。 应改为使用 Azure 门户或 Cloud Shell 来释放你的 DSVM。
> 
> 

有时可能需要处理大规模分析，并需要更多的 CPU、内存或磁盘容量。 如果是这样，可以选择适当的 CPU 核心数、深度学习中基于 GPU 的实例数、内存容量和磁盘类型（包括固态磁盘）来调节 VM 大小，使其满足你的计算和成本需求。 VM 的完整列表及其每小时计算定价见 [Azure 虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)页面。


## <a name="add-more-tools"></a>添加更多工具
DSVM 中预构建的工具可以满足很多常规数据分析需求。 这能节约时间，因为无须一一安装和配置环境。 还能节约成本，因为仅为使用的资源付费。

可以使用本文中介绍的其他 Azure 数据和分析服务改进你的分析环境。 在某些情况下，可能需要额外的工具，包括我们的合作伙伴提供的某些专利工具。 你拥有虚拟机上的完全管理访问权限，可安装必要的新工具。 还可以安装未预安装的 Python 和 R 中的其他程序包。 对于 Python，可以使用 ```conda``` 或 ```pip```。 对于 R，可以在 R 控制台中使用 ```install.packages()```或使用 IDE 并选择“包” > “安装包”。

## <a name="deep-learning"></a>深度学习

除基于框架的示例外，还可获取在 DSVM 上经过验证的一组内容全面的演练。 这些演练可以帮助你快速开始开发图像和文本/语言理解等领域的深度学习应用程序。   


- [在不同的框架中运行神经网络](https://github.com/ilkarman/DeepLearningFrameworks)：本演练展示如何在框架之间迁移代码。 它还展示如何比较模型和各种框架的运行时表现。 

- [生成端到端解决方案以检测图像中的产品的操作指南](https://github.com/Azure/cortana-intelligence-product-detection-from-images)：图像检测是一种能够对图像中的对象进行定位和分类的技术。 这项技术有望在许多现实商业领域带来巨大回报。 例如，零售商可以使用此技术确定客户已从货架上选取哪个产品。 从而，此信息可帮助商店管理产品库存。 

- [音频深度学习](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/):此教程展示如何使用[城市声音数据集](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)训练用于音频事件检测的深度学习模型。 它还提供有关如何处理音频数据的概述。

- [文本文档分类](https://github.com/anargyri/lstm_han)：本演练展示如何生成和训练两种神经网络架构：分层注意网络和长短期记忆 (LSTM) 网络。 这些神经网络使用用于深度学习的 Keras API 对文本文档进行分类。 

## <a name="summary"></a>摘要
本文仅介绍了可在 Microsoft Data Science Virtual Machine 上执行的部分操作。 你还可以执行很多其他操作，使 DSVM 成为有效的分析环境。

