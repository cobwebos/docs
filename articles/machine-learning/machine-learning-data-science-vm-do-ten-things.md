---
title: "数据科研虚拟机的十大功能 | Microsoft Docs"
description: "在数据科研虚拟机上执行各种数据探索和建模任务。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: gokuma;weig;bradsev
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c35d1548262f25e65c391c927919b8acf1411e10
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="ten-things-you-can-do-on-the-data-science-virtual-machine"></a>数据科研虚拟机的十大功能
Microsoft 数据科研虚拟机 (DSVM) 是一个功能强大的数据科研开发环境，使你可以执行各种数据探索和建模任务。 该环境中附带了内置和捆绑的多款主流分析工具，便于你针对本地、云或混合部署快速开始分析。 DSVM 与许多 Azure 服务密切合作，能够读取并处理已存储在 Azure、Azure SQL 数据仓库、Azure Data Lake、Azure 存储或 Azure Cosmos DB 中的数据。 它还可以利用 Azure 机器学习和 Azure 数据工厂等其他分析工具。

在本文中，我们将介绍如何使用 DSVM 执行各种数据科研任务，并与其他 Azure 服务交互。 可以在 DSVM 上执行的操作如下所示：

1. 在 DSVM 上使用 Microsoft R Server、Python 本地探索数据和开发模型
2. 使用 Jupyter Notebook 在浏览器上使用 Python 2、Python 3、Microsoft R（即专用于实现可扩展性和性能的 R 的企业就绪版）实验你的数据
3. 在 Azure 机器学习上运转使用 R 和 Python 生成的模型，以便客户端应用程序使用简单的 Web 服务接口即可访问你的模型
4. 使用 Azure 门户或 PowerShell 管理 Azure 资源
5. 通过将 Azure 文件存储创建为可在 DSVM 上装载的驱动器，即可扩展你的存储空间并跨整个团队共享大型数据集/代码
6. 使用 GitHub 与你的团队共享代码，使用预安装的 Git 客户端访问你的存储库 - Git Bash、Git GUI。
7. 访问各种 Azure 数据和分析服务，如 Azure Blob 存储、Azure Data Lake、Azure HDInsight (Hadoop)、Azure Cosmos DB、Azure SQL 数据仓库和数据库
8. 使用 DSVM 上预安装的 Power BI Desktop 生成报表和仪表板，然后将它们部署到云中
9. 动态缩放 DSVM 即可满足项目需求
10. 在虚拟机上安装其他工具   

> [!NOTE]
> 需要为本文中列出的许多其他数据存储和分析服务支付额外的使用费用。 有关详细信息，请参阅 [Azure 定价](https://azure.microsoft.com/pricing/)页。
> 
> 

**先决条件**

* 需要 Azure 订阅。 可以在[此处](https://azure.microsoft.com/free/)注册免费试用版。
* 有关在 Azure 门户上配置数据科研虚拟机的说明，请参阅[创建虚拟机](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm)。

## <a name="1-explore-data-and-develop-models-using-microsoft-r-server-or-python"></a>1.使用 Microsoft R Server 或 Python 探索数据和开发模型
使用 R 和 Python 之类的语言即可在 DSVM 上执行数据分析。

对于 R，可以使用称为“Revolution R Enterprise 8.0”的 IDE（可在“开始”菜单或桌面上找到）。 Microsoft 额外提供了基于开源/CRAN-R 的库，以便支持可扩展的分析以及使能够分析相较于执行并行分块分析所允许的内存大小更大的数据。 还可以选择安装 R IDE（如 [RStudio](https://www.rstudio.com/products/rstudio-desktop/)）。

对于 Python，可以使用已预安装 Python Tools for Visual Studio (PTVS) 扩展的 Visual Studio Community Edition 之类的 IDE。 默认情况下，仅在 PTVS 上对 Python 2.7 进行最基本的配置（不带 SciKit、Pandas 之类的任何分析库）。 为了支持 Anaconda Python 2.7 和 3.5，需要执行以下操作：

* 按以下方法操作为每个版本创建自定义环境：在 Visual Studio 2015 Community Edition 中，导航到“工具” -> “Python 工具” -> “Python 环境”，然后单击“+ 自定义”
* 提供描述并将环境前缀路径设置为 *c:\anaconda*（适用于 Anaconda Python 2.7）或 *c:\anaconda\envs\py35*（适用于 Anaconda Python 3.5）
* 单击“自动检测”，然后单击“应用”以保存环境。

以下是自定义环境设置在 Visual Studio 中的外观。

![PTVS 设置](./media/machine-learning-data-science-vm-do-ten-things/PTVSSetup.png)

有关如何创建 Python 环境的更多详细信息，请参阅 [PTVS 文档](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it)。

现在即可开始创建新的 Python 项目。 导航到“文件” -> “新建” -> “项目” -> “Python”，然后选择要生成的 Python 应用程序的类型。 可以将当前项目的 Python 环境设置为所需版本（Anaconda 2.7 或 3.5）：右键单击“Python 环境”、选择“添加/删除 Python 环境”，然后选择要与该项目关联的所需环境。 有关使用 PTVS 的更多详细信息，请参阅产品[文档](https://github.com/Microsoft/PTVS/wiki)页。

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2.借助 Jupyter Notebook，可使用 Python 或 R 探索数据并建模
Jupyter Notebook 是一个功能强大的环境，为数据探索和建模提供基于浏览器的“IDE”。 可以在 Jupyter Notebook 中使用 Python 2、Python 3 或 R（开源和 Microsoft R Server）。

若要启动 Jupyter Notebook，请单击标题为“Jupyter Notebook”的“开始”菜单图标/桌面图标。 在 DSVM 上，还可以浏览到“https://localhost:9999/”来访问 Jupiter Notebook。 如果它提示你输入密码，请按照[配置 Microsoft 数据科研虚拟机](machine-learning-data-science-provision-vm.md)主题的***如何在 Jupyter Notebook 服务器上创建强密码***部分中提供的说明操作，即可创建用于访问 Jupyter Notebook 的强密码。 

打开 Notebook 后，将看到一个目录，其中包含了预打包到 DSVM 中的几个 Notebook 示例。 现在你可以：

* 单击要查看代码的 Notebook。
* 按 **SHIFT-ENTER** 执行每个单元。
* 依次单击“单元” -> “运行”运行整个 Notebook
* 按以下方法操作创建新的 Notebook：单击 Jupyter 图标（左上角）、单击右侧的“新建”按钮，然后选择 Notebook 语言（也称为内核）。   

> [!NOTE]
> 我们目前支持 Python 2.7、Python 3.5 和 R。R 内核支持在开源 R 以及企业可扩展 Microsoft R Server 中进行编程。   
> 
> 

打开 Notebook 后，即可浏览数据、生成模型、使用所选的库测试模型。

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3.使用 R 或 Python 生成模型，使用 Azure 机器学习对模型进行操作
模型生成并经验证后，下一步通常是将它部署到生产环境。 这使你的客户端应用程序可以实时或基于批处理模式调用模型预测。 Azure 机器学习提供一种机制，可用于操作使用 R 或 Python 生成的模型。

当在 Azure 机器学习中操作模型时，会公开 Web 服务，允许客户端在输入参数中传递 REST 调用，以及以输出形式接收模型中的预测。   

> [!NOTE]
> 如果尚未注册 Azure 机器学习，通过访问 [Azure 机器学习工作室](https://studio.azureml.net/)主页并单击“开始使用”，即可获取免费工作区或标准工作区。   
> 
> 

### <a name="build-and-operationalize-python-models"></a>生成和操作 Python 模型
以下是一段在 Python Jupyter Notebook 中开发的代码，它使用 SciKit-learn 库生成一个简单模型。

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

用于将你的 Python 模型部署到 Azure 机器学习的方法将模型的预测封装到函数中，然后使用预安装的 Azure 机器学习 Python 库提供的属性对它进行修饰，这些属性表示你的 Azure 机器学习工作区 ID、API 密钥以及输入和返回参数。  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

客户端现在可以调用 Web 服务。 有方便的封装器，可用于构建 REST API 请求。 以下是使用 Web 服务的示例代码。

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> 目前，Azure 机器学习库仅在 Python 2.7 上受支持。   
> 
> 

### <a name="build-and-operationalize-r-models"></a>生成和操作 R 模型
可以将数据科研虚拟机或其他平台上生成的 R 模型部署到 Azure 机器学习，所用方式与 Python 的部署方式类似。 步骤如下所示：

* 创建如下所示的 settings.json 文件，提供你的工作区 ID 和身份验证令牌。
* 编写适用于模型的预测函数的封装器。
* 调用 Azure 机器学习库中的 ```publishWebService```，传入函数封装器。  

以下是可用于在 Azure 机器学习中设置、生成、发布和使用模型为 Web 服务的过程和代码片段。

#### <a name="setup"></a>设置
1. 通过在 Revolution R Enterprise 8.0 IDE 或 R IDE 中键入 ```install.packages("AzureML")```，即可安装机器学习 R 程序包。
2. 从[此处](https://cran.r-project.org/bin/windows/Rtools/)下载 RTools。 若要使 R 程序包在机器学习中运行，路径中需要 zip 实用程序（命名为 zip.exe）。
3. 在主目录下名为 ```.azureml``` 的目录下创建 settings.json 文件，然后输入 Azure 机器学习工作区的参数：

settings.json 文件结构：

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>使用 R 生成模型，并在 Azure 机器学习中发布它
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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>使用部署在 Azure 机器学习中的模型
若要从客户端应用程序使用模型，需要使用 Azure 机器学习库按使用 `services` API 调用的名称查找已发布的 Web 服务，确定终结点。 然后只需调用 `consume` 函数，并传入要预测的数据帧。
以下代码用于使用已发布为 Azure 机器学习 Web 服务的模型。

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

可以在[此处](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf)找到有关 Azure 机器学习 R 库的详细信息。

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4.使用 Azure 门户或 PowerShell 管理 Azure 资源
DSVM 不仅允许你在虚拟机上本地生成分析解决方案，而且还允许你访问 Microsoft Azure 云上的服务。 Azure 提供多个计算、存储、数据分析服务和其他服务，可以从你的 DSVM 管理并访问这些服务。

若要管理 Azure 订阅和云资源，可以使用浏览器并指向 [Azure 门户](https://portal.azure.com)。 还可以使用 Azure Powershell 通过脚本管理 Azure 订阅和资源。
可以从标题为“Microsoft Azure Powershell”的桌面快捷方式或从“开始”菜单，运行 Azure PowerShell。 有关如何使用 Windows Powershell 脚本管理 Azure 订阅和资源的详细信息，请参阅 [Microsoft Azure Powershell 文档](../powershell-azure-resource-manager.md)。

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5.使用共享文件系统扩展存储空间
数据科学家可以在团队内共享大型数据集、代码或其他资源。 DSVM 自身具有大约 70GB 可用空间。 若要扩展存储，可以使用 Azure 文件服务，然后将它装载到 DSVM 或通过 REST API 访问它。   

> [!NOTE]
> Azure 文件服务共享的最大空间为 5TB，单个文件大小限制为 1TB。   
> 
> 

可以使用 Azure PowerShell 创建 Azure 文件服务共享。 以下是在 Azure PowerShell 下运行，用于创建 Azure 文件服务共享的脚本。

    # Authenticate to Azure.
    Login-AzureRmAccount
    # Select your subscription
    Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
    # Create a new resource group.
    New-AzureRmResourceGroup -Name <dsvmdatarg>
    # Create a new storage account. You can reuse existing storage account if you wish.
    New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
    # Set your current working storage account
    Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

    # Create a Azure File Service Share
    $s = New-AzureStorageShare <<teamsharename>>
    # Create a directory under the FIle share. You can give it any name
    New-AzureStorageDirectory -Share $s -Path <directory name>
    # List the share to confirm that everything worked
    Get-AzureStorageFile -Share $s


现在，已创建了 Azure 文件共享，可以将它装载到 Azure 中的任何虚拟机中。 强烈建议将 VM 和存储帐户置于同一 Azure 数据中心，以避免延迟和产生数据传输费用。 以下命令可以将驱动器装载到可以在 Azure Powershell 上运行的 DSVM。

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


现在可以像访问 VM 上的任何正常驱动器一样访问此驱动器。

## <a name="6-share-code-with-your-team-using-github"></a>6.使用 GitHub 与团队共享代码
GitHub 是一个代码存储库，可以在其中找到其他工具的许多示例代码和资源，这些工具使用了开发人员社区共享的各种技术。 它使用 Git 作为跟踪和存储代码文件版本的技术。 GitHub 也是一个平台，可以在其中创建你自己的存储库来存储团队的共享代码和文档、实现版本控制，还可以控制谁有权查看和贡献代码。 有关使用 Git 的详细信息，请访问 [GitHub 帮助页](https://help.github.com/)。 可以将 GitHub 用作与团队协作、使用社区开发的代码以及重新贡献代码给社区的方法之一。

DSVM 已在命令行以及 GUI 上加载了用于访问 GitHub 存储库的客户端工具。 使用 Git 和 GitHub 的命令行工具称为 Git Bash。 DSVM 上已安装的 Visual Studio 具有 Git 扩展。 可以在“开始”菜单和桌面上找到这些工具的启动图标。

若要从 GitHub 存储库下载代码，请使用 ```git clone``` 命令。 例如，若要将 Microsoft 发布的数据科研存储库下载到当前目录，请在使用的 ```git-bash``` 中运行以下命令。

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

在 Visual Studio 中，可以执行相同的克隆操作。 以下屏幕截图演示了如何在 Visual Studio 中访问 Git 和 GitHub 工具。

![Visual Studio 中的 Git](./media/machine-learning-data-science-vm-do-ten-things/VSGit.PNG)

可以从 github.com 上提供的多种资源中找到有关使用 Git 操作 GitHub 存储库的更多信息。 [备忘单](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf)是一个有用的参考。

## <a name="7-access-various-azure-data-and-analytics-services"></a>7.访问各种 Azure 数据和分析服务
### <a name="azure-blob"></a>Azure Blob
Azure Blob 是适合大小数据的经济可靠云存储。 了解如何移动数据到 Azure Blob 以及访问 Azure Blob 中存储的数据。

**先决条件**

* **从 [Azure 门户](https://portal.azure.com)创建 Azure Blob 存储帐户。**

![Create_Azure_Blob](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)

* 确认在 ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` 找到了预安装的命令行工具 AzCopy。 可以将包含 azcopy.exe 的目录添加到 PATH 环境变量，以避免在运行此工具时键入完整的命令路径。 有关 AzCopy 工具的详细信息，请参阅 [AzCopy 文档](../storage/storage-use-azcopy.md)
* 启动 Azure 存储资源管理器工具。 可以从 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)下载该工具。 

![AzureStorageExplorer_v4](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_v4.png)

**将数据从 VM 移动到 Azure Blob：AzCopy**

若要在本地文件和 Blob 存储之间移动数据，可以在命令行或 PowerShell 中使用 AzCopy：

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

将 **C:\myfolder** 替换为存储有你的文件的路径、将 **mystorageaccount** 替换为 Blob 存储帐户名称、将 **mycontainer** 替换为容器名称、将 **storage account key** 替换为 Blob 存储访问密钥。 可以在 [Azure 门户](https://portal.azure.com)中找到你的存储帐户凭据。

![StorageAccountCredential_v2](./media/machine-learning-data-science-vm-do-ten-things/StorageAccountCredential_v2.png)

在 PowerShell 中或从命令提示符下，运行 AzCopy 命令。 以下是 AzCopy 命令的一些使用示例：

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



在运行 AzCopy 命令向 Azure 存储进行复制后，你会看到文件立即出现在 Azure 存储资源管理器中。

![AzCopy_run_finshed_Storage_Explorer_v3](./media/machine-learning-data-science-vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**将数据从 VM 移动到 Azure Blob：Azure 存储资源管理器**

还可以在 VM 中使用 Azure 存储资源管理器上传本地文件中的数据：

* 若要将数据上传到容器，请选择目标容器，然后单击“上传”按钮。![在存储资源管理器中上传](./media/machine-learning-data-science-vm-do-ten-things/storage-accounts.png)
* 单击“文件”框右侧的“...”，选择要从文件系统上传的一个或多个文件，然后单击“上传”开始上传文件。![将文件上传到 Blob](./media/machine-learning-data-science-vm-do-ten-things/upload-files-to-blob.png)

**从 Azure Blob 读取数据：机器学习读取器模块**

在 Azure 机器学习工作室中，可以使用**导入数据模块**读取 Blob 中的数据。

![AML_ReaderBlob_Module_v3](./media/machine-learning-data-science-vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**读取 Azure Blob 中的数据：Python ODBC**

在 Jupyter Notebook 或 Python 程序中，可以使用 **BlobService** 库直接读取 Blob 中的数据。

首先，导入所需的程序包：

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

然后插入你的 Azure Blob 帐户凭据，读取 Blob 中的数据：

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

    #unzipping downloaded files if needed
    #with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
    #    z.extractall(LOCALDIRECTORY)

    df1 = pd.read_csv(LOCALFILE, header=0)
    df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
    print 'the size of the data is: %d rows and  %d columns' % df1.shape

数据作为数据帧读入：

![IPNB_data_readin](./media/machine-learning-data-science-vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake 存储是一个超大规模存储库，适用于大数据分析工作负荷，并且与 Hadoop 分布式文件系统 (HDFS) 兼容。 它适用于 Hadoop 生态系统和 Azure Data Lake Analytics。 我们演示了如何将数据移动到 Azure Data Lake Store，以及如何使用 Azure Data Lake Analytics 运行分析。

**先决条件**

* 在 [Azure 门户](https://portal.azure.com)中创建 Azure Data Lake Analytics。

![Azure_Data_Lake_Create_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* 在位于虚拟机中的 Visual Studio Community Edition 上，已安装在此[链接](https://www.microsoft.com/download/details.aspx?id=49504)处找到的 **Visual Studio** 中的 **Azure Data Lake 工具**。 在启动 Visual Studio 并登录你的 Azure 订阅后，会在 Visual Studio 的左侧看到你的 Azure Data Analytics 帐户和存储。

![Azure_Data_Lake_PlugIn_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**将数据从 VM 移动到 Data Lake：Azure Data Lake 资源管理器**

可以使用 **Azure Data Lake 资源管理器**将虚拟机的本地文件中的数据上传到 Data Lake 存储。

![Azure_Data_Lake_UploadData](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

还可以使用 [Azure 数据工厂 (ADF)](https://azure.microsoft.com/services/data-factory/) 生成用于实现与 Azure Data Lake 之间移动数据的数据管道。 有关介绍如何生成数据管道的步骤，请参阅此[文章](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/)。

**将数据从 Azure Blob 读取到 Data Lake：U-SQL**

如果数据驻留在 Azure Blob 存储中，可以使用 U-SQL 查询直接读取 Azure 存储 Blob 中的数据。 编写 U-SQL 查询之前，确保你的 Blob 存储帐户已链接到 Azure Data Lake。 转到 **Azure 门户**、找到 Azure Data Lake Analytics 仪表板、单击“添加数据源”、选择 **Azure 存储**的存储类型，然后插入你的 Azure 存储帐户名称和密钥。 然后，将可以引用存储帐户中存储的数据。

![输入存储帐户和密钥](./media/machine-learning-data-science-vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

在 Visual Studio 中，可以读取 Blob 存储中的数据、执行一些数据操作、特征工程，以及将生成的数据输出到 Azure Data Lake 或 Azure Blob 存储。 在引用 Blob 存储中的数据时，请使用 **wasb://**；在引用 Azure Data Lake 中的数据时，请使用 **swbhdfs://**

![数据帧](./media/machine-learning-data-science-vm-do-ten-things/USQL_Read_Blob_v2.PNG)

在 Visual Studio 中，可以使用以下 U-SQL 查询：

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



在将查询提交到服务器后，将显示表明作业状态的图表。

![作业状态关系图](./media/machine-learning-data-science-vm-do-ten-things/USQL_Job_Status.PNG)

**查询 Data Lake 中的数据：U-SQL**

在数据集引入 Azure Data Lake 后，即可使用 [U-SQL 语言](../data-lake-analytics/data-lake-analytics-u-sql-get-started.md)查询和浏览数据。 U-SQL 语言与 T-SQL 类似，但结合了 C＃ 的一些功能，以便用户可以编写自定义的模块、用户定义的函数等。可以使用上一步骤中的脚本。

在将查询提交到服务器后，可以很快在 **Azure Data Lake 资源管理器**中找到 tripdata_summary.CSV，可以通过右键单击该文件来预览数据。

![Azure Data Lake 资源管理器中的文件](./media/machine-learning-data-science-vm-do-ten-things/USQL_create_summary.png)

若要查看文件信息，请执行以下步骤：

![文件摘要](./media/machine-learning-data-science-vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop 群集
Azure HDInsight 是云上托管的 Apache Hadoop、Spark、HBase 和 Storm 服务。 可以轻松使用数据科研虚拟机中的 Azure HDInsight 群集。

**先决条件**

* 从 [Azure 门户](https://portal.azure.com)创建 Azure Blob 存储帐户。 此存储帐户用于存储 HDInsight 群集的数据。

![创建 Azure Blob 存储帐户](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)

* 从 [Azure 门户](machine-learning-data-science-customize-hadoop-cluster.md)自定义 Azure HDInsight Hadoop 群集
  
  * 必须在创建 HDInsight 群集时将其与已创建的存储帐户相链接。 此存储帐户用于访问可在群集中处理的数据。

![指向使用 HDInsight 群集创建的存储帐户的链接](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_v4.PNG)

* 在创建群集后，必须启用对其头节点的**远程访问**。 记住在此处指定的远程访问凭据（与创建时为群集指定的远程访问凭据不同）：稍后你将需要它们。

![启用远程访问](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* 创建 Azure 机器学习工作区。 机器学习实验将存储在此机器学习工作区中。 选择门户中突出显示的选项，如以下屏幕截图中所示。

![创建 Azure 机器学习工作区](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space.PNG)

* 然后输入工作区的参数

![输入机器学习工作区参数](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* 使用 IPython Notebook 上传数据 首先导入所需程序包、插入凭据、在你的存储帐户中创建数据库，然后将数据上传到 HDI 群集。

        #Import required Packages
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


        #Create the connection to Hive using ODBC
        SERVER_NAME='xxx.azurehdinsight.net'
        DATABASE_NAME='nyctaxidb'
        USERID='xxx'
        PASSWORD='xxxx'
        DB_DRIVER='Microsoft Hive ODBC Driver'
        driver = 'DRIVER={' + DB_DRIVER + '}'
        server = 'Host=' + SERVER_NAME + ';Port=443'
        database = 'Schema=' + DATABASE_NAME
        hiveserv = 'HiveServerType=2'
        auth = 'AuthMech=6'
        uid = 'UID=' + USERID
        pwd = 'PWD=' + PASSWORD
        CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
        connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
        cursor=connection.cursor()


        #Create Hive database and tables
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


        #Upload data from blob storage to HDI cluster
        for i in range(1,13):
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
            cursor.execute(queryString)
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
            cursor.execute(queryString)


* 或者，可以遵循此[演练](machine-learning-data-science-process-hive-walkthrough.md)将纽约出租车数据上传到 HDI 群集。 主要步骤如下所示：
  
  * AzCopy：将公共 Blob 中的压缩 CSV 下载到本地文件夹中
  * AzCopy：将本地文件夹中的未压缩 CSV 上传到 HDI 群集
  * 登录到 Hadoop 群集的头节点，并为探索数据分析做好准备

在数据加载到 HDI 群集后，可以在 Azure 存储资源管理器中检查数据。 并且在 HDI 群集中创建数据库 nyctaxidb。

**数据探索：Python 中的 Hive 查询**

由于数据在 Hadoop 群集中，因此可以 pyodbc 包连接到 Hadoop 群集并使用 Hive 查询数据库，以执行探索和特征工程。 可以查看我们在先决条件步骤中创建的现有表。

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![查看现有表](./media/machine-learning-data-science-vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

请查看每月的记录数以及行程表中已付小费或未付小费的频率：

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![每个月的记录数的走势图](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

    queryString = """
        SELECT tipped, COUNT(*) AS tip_freq
        FROM
        (
            SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
            FROM nyctaxidb.fare
        )tc
        GROUP BY tipped;
        """
    results = pd.read_sql(queryString,connection)

    results.columns = ['tipped', 'trip_count']
    df = results.copy()
    df.index = df['tipped']
    df['trip_count'].plot(kind='bar')


![行程频率图](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

我们还可以计算上车位置和下车位置之间的距离，然后将该距离与行程距离比较。

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
    results = pd.read_sql(queryString,connection)
    results.head(5)


![上车和下车表](./media/machine-learning-data-science-vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![上车/下车距离与行程距离的对比图](./media/machine-learning-data-science-vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

现在，请准备一组低采样 (1%) 的数据用于建模。 我们可以在机器学习读取器模块中使用此数据。

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

        --- now insert contents of the join into the above internal table

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

不久，即可看到数据已载入Hadoop 群集：

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![数据表](./media/machine-learning-data-science-vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**使用机器学习从 HDI 读取数据：读取器模块**

还可以在机器学习工作室中使用**读取器**模块访问 Hadoop 群集中的数据库。 插入你的 HDI 群集和 Azure 存储帐户的凭据，将能够使用 HDI 群集中的数据库生成机器学习模型。

![读取器模块属性](./media/machine-learning-data-science-vm-do-ten-things/AML_Reader_Hive.PNG)

然后，可以查看已评分的数据集：

![查看已评分数据集](./media/machine-learning-data-science-vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Azure SQL 数据仓库和数据库
Azure SQL 数据仓库是一项弹性数据仓库即服务，具有企业级 SQL Server 体验。

可以按照本[文章](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)中提供的说明设置 Azure SQL 数据仓库。 完成设置 Azure SQL 数据仓库后，即可使用此[演练](machine-learning-data-science-process-sqldw-walkthrough.md)，来使用 SQL 数据仓库中的数据执行数据上传、探索和建模。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB 是云中的 NoSQL 数据库。 它允许你使用 JSON 等文档，还允许你存储和查询文档。

若要从 DSVM 访问 Azure Cosmos DB，需要执行以下每个必要步骤。

1. 安装 DocumentDB Python SDK（在命令提示符下运行 ```pip install pydocumentdb```）
2. 在 [Azure 门户](https://portal.azure.com)中，创建 Azure Cosmos DB 帐户和 DocumentDB 数据库
3. 从[此处](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d)下载“Azure Cosmos DB 迁移工具”，然后解压缩到所选目录
4. 使用迁移工具（安装 Cosmos DB 迁移工具所在目录中的 dtui.exe）的以下命令参数将[公共 blob](https://cahandson.blob.core.windows.net/samples/volcano.json) 上存储的 JSON 数据（火山数据）导入 Cosmos DB。 输入以下源和目标位置参数。
   
    /s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

导入数据后，可以转到 Jupyter 并打开标题为 *DocumentDBSample* 的 Notebook，其中包含用于访问 DocumentDB 和执行一些基本查询的 python 代码。 通过访问 DocumentDB 的[文档页](https://azure.microsoft.com/documentation/learning-paths/documentdb/)可以了解有关该服务的详细信息。

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8.使用 Power BI Desktop 生成报表和仪表板
让我们在 Power BI 中可视化我们在上述 Cosmos DB 示例中看到的火山 JSON 文件，以获取对数据的视觉洞察。 [Power BI 文章](../documentdb/documentdb-powerbi-visualize.md)中提供了详细步骤。 大致步骤如下所示：

1. 打开 Power BI Desktop 并执行“获取数据”。 指定如下 URL：https://cahandson.blob.core.windows.net/samples/volcano.json
2. 应该将导入的 JSON 记录看作列表
3. 将列表转换为表，以便 Power BI 可以处理该表
4. 通过单击展开图标（在列右侧有“左箭头和右箭头”图标的展开图标）展开列
5. 请注意，位置是一个“记录”字段。 展开记录并仅选择坐标。 坐标是一个列表列
6. 添加新的列，以使用公式 ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` 将列表坐标列转换为逗号分隔的经纬度列（连接坐标列表字段中的两个元素）。
7. 最后，将 ```Elevation``` 列转换为小数，然后选择“关闭”和“应用”。

不执行上述步骤，而是将上述步骤脚本化的以下代码粘贴到 Power BI 中的高级编辑器（允许使用查询语言编写数据转换）。

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



现在，Power BI 数据模型中有数据。 Power BI Desktop 的外观应如下所示。

![Power BI Desktop](./media/machine-learning-data-science-vm-do-ten-things/PowerBIVolcanoData.png)

可以使用数据模型开始生成报表和可视化效果。 可以遵循此 [Power BI 文章](../documentdb/documentdb-powerbi-visualize.md#build-the-reports)中的步骤生成报表。 最终结果将是如下所示的报表。

![Power BI Desktop 报表视图 - Power BI 连接器](./media/machine-learning-data-science-vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9.动态缩放 DSVM 即可满足项目需求
可以增加和减少 DSVM 以满足项目需求。 如果不需要在晚上或周末使用 VM，只需从 [Azure 门户](https://portal.azure.com)关闭 VM。

> [!NOTE]
> 如果仅使用 VM 上的操作系统关闭按钮，会产生计算费用。  
> 
> 

如果需要处理一些大型分析以及需要更多 CPU 和/或内存和/或磁盘容量，可以基于 CPU 内核、内存容量和磁盘类型（包括固态驱动器）找到满足你的计算和预算需求的许多可选 VM 大小。 可在 [Azure 虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)页上找到 VM 及其每小时计算定价的完整列表。

同样地，如果你对 VM 处理能力的需求减少（例如：已将主要工作负荷移动到 Hadoop 或 Spark 群集），可以通过在 [Azure 门户](https://portal.azure.com)中转到 VM 实例设置，来减少群集。 屏幕截图如下所示。

![VM 实例设置](./media/machine-learning-data-science-vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10.在虚拟机上安装其他工具
我们打包了多个工具，我们认为这些工具将能够解决许多常规数据分析需求，并且应该能节省你的时间（避免不得不依次安装并配置环境）以及费用（只支付所用资源的费用）。

可以利用本文中介绍的其他 Azure 数据和分析服务来增强分析环境。 我们了解，在某些情况下你的需求可能需要其他工具（包括一些专用第三方工具）。 你在虚拟机上具有完全的管理访问权限，可安装所需的新工具。 还可以安装未预安装的 Python 和 R 中的其他程序包。 对于 Python，可以使用 ```conda``` 或 ```pip```。 对于 R，可以在 R 控制台中使用 ```install.packages()```，或者使用 IDE 并依次选择“程序包” -> “安装程序包...”。

## <a name="summary"></a>摘要
这些只是 Microsoft 数据科研虚拟机上的一部分功能。 有更多使它成为有效分析环境的功能。


