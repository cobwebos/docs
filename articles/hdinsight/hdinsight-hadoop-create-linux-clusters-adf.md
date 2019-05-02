---
title: '教程：使用数据工厂在 Azure HDInsight 中创建按需 Apache Hadoop 群集 '
description: 了解如何使用 Azure 数据工厂在 HDInsight 中创建按需 Apache Hadoop 群集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/18/2019
ms.openlocfilehash: 37c9a11b806ff49fce27120d03f67182037dc693
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726882"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>教程：使用 Azure 数据工厂在 HDInsight 中创建按需 Apache Hadoop 群集
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

本文介绍如何使用 Azure 数据工厂在 Azure HDInsight 中按需创建 [Apache Hadoop](https://hadoop.apache.org/) 群集。 然后使用 Azure 数据工厂中的数据管道运行 Hive 作业并删除该群集。 本教程结束时，你便知道如何将大数据作业运行操作化，其中的群集创建、作业运行和群集删除操作都是按计划执行的。

本教程涵盖以下任务： 

> [!div class="checklist"]
> * 创建 Azure 存储帐户
> * 了解 Azure 数据工厂活动
> * 使用 Azure 门户创建数据工厂
> * 创建链接服务
> * 创建管道
> * 触发管道
> * 监视管道
> * 验证输出

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备组件

* PowerShell [Az 模块](https://docs.microsoft.com/powershell/azure/overview)安装。

* 一个 Azure Active Directory 服务主体。 创建服务主体后，请务必使用链接文章中的说明检索**应用程序 ID** 和**身份验证密钥**。 在本教程后面的步骤中，你需要用到这些值。 另外，请确保此服务主体是订阅“参与者”角色的成员，或创建群集的资源组的成员。 有关检索所需值和分配适当角色的说明，请参阅[创建 Azure Active Directory 服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="create-preliminary-azure-objects"></a>创建初始 Azure 对象

在本部分中，您将创建将用于创建按需 HDInsight 群集的各种对象。 创建的存储帐户将包含该示例[HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)脚本 (`hivescript.hql`)，用于模拟示例[Apache Hive](https://hive.apache.org/)在群集运行的作业。

本部分使用 Azure PowerShell 脚本创建存储帐户，并在存储帐户中复制所需的文件。 本部分中的 Azure PowerShell 示例脚本执行以下任务：

1. 登录到 Azure。
2. 创建 Azure 资源组。
3. 创建 Azure 存储帐户。
4. 在存储帐户中创建 Blob 容器
5. 将示例 HiveQL 脚本 (**hivescript.hql**) 复制到 Blob 容器。 [https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql) 上提供了该脚本。 该示例脚本已在另一个公共 Blob 容器中提供。 以下 PowerShell 脚本将这些文件复制到它创建的 Azure 存储帐户。

> [!WARNING]  
> 存储帐户类型`BlobStorage`不能用于 HDInsight 群集。

**使用 Azure PowerShell 创建存储帐户并复制文件：**

> [!IMPORTANT]  
> 指定将通过脚本创建的 Azure 资源组和 Azure 存储帐户的名称。
> 记下脚本所输出的“资源组名称”、“存储帐户名称”和“存储帐户密钥”。 下一部分需要它们。

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

**验证是否已创建存储帐户**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左窗格中选择“资源组”。
3. 选择在 PowerShell 脚本中创建的资源组名称。 如果列出了太多资源组，请使用筛选器。
4. 除非与其他项目共享了资源组，否则“资源”磁贴中会列出一个资源。 该资源就是在前面你为其指定名称的存储帐户。 选择存储帐户名称。
5. 选择“Blob”磁贴。
6. 选择“adfgetstarted”容器。 此时会显示一个名为 **hivescripts** 的文件夹。
7. 打开该文件夹，确保其中包含示例脚本文件 **hivescript.hql**。

## <a name="understand-the-azure-data-factory-activity"></a>了解 Azure 数据工厂活动

[Azure 数据工厂](../data-factory/introduction.md)可以协调和自动化数据的移动和转换。 Azure 数据工厂可以实时创建 HDInsight Hadoop 群集来处理输入数据切片，并在处理完成后删除群集。 

在 Azure 数据工厂中，一个数据工厂可以有一个或多个数据管道。 一个数据管道有一个或多个活动。 有两种类型的活动：

- [数据移动活动](../data-factory/copy-activity-overview.md) - 使用数据移动活动可将数据从源数据存储移到目标数据存储。
- [数据转换活动](../data-factory/transform-data.md)。 可以使用数据转换活动来转换/处理数据。 HDInsight Hive 活动是数据工厂支持的转换活动之一。 用户在本教程中使用 Hive 转换活动。

在本文中，我们将配置 Hive 活动，以创建按需 HDInsight Hadoop 群集。 运行活动来处理数据时，会发生以下情况：

1. 自动为用户实时创建 HDInsight Hadoop 群集，以便处理该切片。 

2. 通过在群集上运行 HiveQL 脚本处理输入数据。 在本教程中，与 Hive 活动关联的 HiveQL 脚本执行以下操作：

    - 使用现有表 (*hivesampletable*) 创建另一个表 **HiveSampleOut**。
    - 只在 **HiveSampleOut** 表中填充原始 *hivesampletable* 中的特定列。
    
3. HDInsight Hadoop 群集在处理完成后删除，群集空闲时间为配置的时间（timeToLive 设置）。 如果在这段 timeToLive 空闲时间内可以处理下一数据切片，则会使用同一群集处理该切片。  

## <a name="create-a-data-factory"></a>创建数据工厂

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 从左侧菜单中，导航到 **+ 创建资源** > **Analytics** > **数据工厂**。

    ![门户中的 Azure 数据工厂](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "门户中的 Azure 数据工厂")

3. 输入或选择以下值：**新建数据工厂**磁贴：

    |属性  |值  |
    |---------|---------|
    |名称 | 输入数据工厂的名称。 该名称必须全局唯一。|
    |订阅 | 选择 Azure 订阅。 |
    |资源组 | 选择“使用现有项”，然后选择使用 PowerShell 脚本创建的资源组。 |
    |Version | 将保留**V2**。 |
    |Location | 位置会自动设置为先前在创建资源组时指定的位置。 在本教程中，位置设置为“美国东部”。 |

    ![使用 Azure 门户创建 Azure 数据工厂](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "使用 Azure 门户创建 Azure 数据工厂")

4. 选择“创建”。 创建一个数据工厂可能需要花费 2 到 4 分钟。

5. 创建数据工厂后，你将收到**部署成功**通知**转到资源**按钮。  选择“转到资源”以打开数据工厂默认视图。

6. 选择“创作和监视”启动 Azure 数据工厂创作和监视门户。

    ![Azure 数据工厂概述](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Azure 数据工厂概述")

## <a name="create-linked-services"></a>创建链接服务

在本部分，我们将在数据工厂中创作两个链接服务。

- 一个用于将 Azure 存储帐户链接到数据工厂的 **Azure 存储链接服务**。 按需 HDInsight 群集使用此存储。 它还包含群集上运行的 Hive 脚本。
- 一个**按需 HDInsight 链接服务**。 Azure 数据工厂自动创建 HDInsight 群集并运行 Hive 脚本。 然后，当群集空闲预配置的时间后，就会删除 HDInsight 群集。

### <a name="create-an-azure-storage-linked-service"></a>创建 Azure 存储链接服务

1. 从左窗格**让我们开始吧**页上，选择**作者**图标。

    ![创建 Azure 数据工厂链接服务](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "创建 Azure 数据工厂链接服务")

2. 在窗口左下角选择“连接”，然后选择“+新建”。

    ![在 Azure 数据工厂中创建连接](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "在 Azure 数据工厂中创建连接")

3. 在“新建链接服务”对话框中选择“Azure Blob 存储”，然后选择“继续”。

    ![为数据工厂创建 Azure 存储链接服务](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "为数据工厂创建 Azure 存储链接服务")

4. 提供的存储链接服务的以下值：

    |属性 |值 |
    |---|---|
    |名称 |输入 `HDIStorageLinkedService` 。|
    |Azure 订阅 |从下拉列表中选择你的订阅。|
    |存储帐户名称 |选择你创建的 Azure 存储帐户作为 PowerShell 脚本的一部分。|

    然后选择“完成”。

    ![提供 Azure 存储链接服务的名称](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "提供 Azure 存储链接服务的名称")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>创建按需 HDInsight 链接服务

1. 再次选择“+ 新建”按钮，创建另一个链接服务。

2. 在中**新建链接服务**窗口中，选择**计算**选项卡。

3. 选择**Azure HDInsight**，然后选择**继续**。

    ![为 Azure 数据工厂创建 HDInsight 链接服务](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "为 Azure 数据工厂创建 HDInsight 链接服务")

4. 在中**新建链接服务**窗口中，输入以下值并保留为默认值的其余部分：

    | 属性 | 值 |
    | --- | --- |
    | 名称 | 输入 `HDinisghtLinkedService` 。|
    | Type | 选择**按需 HDInsight**。 |
    | Azure 存储链接服务 | 选择 `HDIStorageLinkedService`。 |
    | 群集类型 | 选择“hadoop” |
    | 生存时间 | 提供在自动删除 HDInsight 群集之前希望该群集保留的持续时间。|
    | 服务主体 ID | 提供的部分先决条件创建的 Azure Active Directory 服务主体的应用程序 ID。 |
    | 服务主体密钥 | 提供 Azure Active Directory 服务主体的身份验证密钥。 |
    | 群集名称前缀 | 向由数据工厂创建的所有群集类型提供一个值，作为前缀。 |
    |订阅 |从下拉列表中选择你的订阅。|
    | 选择资源组 | 选择前面使用的 PowerShell 脚本的一部分创建的资源组。|
    |选择地区 | 从下拉列表中选择一个区域。|
    | OS 类型/群集的 SSH 用户名 | 输入 SSH 用户名，常用`sshuser`。 |
    | OS 类型中的群集 SSH 密码 | 提供 SSH 用户的密码 |
    | OS 类型中的群集用户名称 | 输入群集用户的用户名，常用`admin`。 |
    | OS 类型中的群集用户密码 | 提供有关群集用户密码。 |

    然后选择“完成”。

    ![提供 HDInsight 链接服务的值](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "提供 HDInsight 链接服务的值")

## <a name="create-a-pipeline"></a>创建管道

1. 选择“+ (加)”按钮，然后选择“管道”。

    ![在 Azure 数据工厂中创建管道](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "在 Azure 数据工厂中创建管道")

2. 在“活动”工具箱中展开“HDInsight”，将“Hive”活动拖放到管道设计器图面。 在“常规”选项卡中，提供活动的名称。

    ![将活动添加到数据工厂管道](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "将活动添加到数据工厂管道")

3. 请确保已选择的 Hive 活动，请选择**HDI 群集**选项卡上，并从**HDInsight 链接服务**下拉列表中，选择链接服务，您之前创建**HDinightLinkedService**，用于 HDInsight。

    ![为管道提供 HDInsight 群集详细信息](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "为管道提供 HDInsight 群集详细信息")

4. 选择“脚本”选项卡并完成以下步骤：

    1. 有关**脚本链接服务**，选择**HDIStorageLinkedService**从下拉列表。 此值为先面创建的存储链接服务。

    1. 对于“文件路径”，请选择“浏览存储”并导航到示例 Hive 脚本所在的位置。 如果先前运行了 PowerShell 脚本，则此位置应是 `adfgetstarted/hivescripts/hivescript.hql`。

        ![为管道提供 Hive 脚本详细信息](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "为管道提供 Hive 脚本详细信息")

    1. 在“高级” > “参数”下，选择“从脚本自动填充”。 此选项会在 Hive 脚本中查找需要在运行时提供值的所有参数。 使用的脚本 (**hivescript.hql**) 包含 **Output** 参数。 提供**值**格式`wasb://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/`使其指向 Azure 存储的现有文件夹。 该路径区分大小写。 这是脚本的输出的存储路径。
    
        ![提供 Hive 脚本的参数](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "提供 Hive 脚本的参数")

1. 选择“验证”以验证管道。 选择 **>>**（右键头）按钮，关闭验证窗口。

    ![验证 Azure 数据工厂管道](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "验证 Azure 数据工厂管道")

1. 最后，选择“全部发布”，将项目发布到 Azure 数据工厂。

    ![发布 Azure 数据工厂管道](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "发布 Azure 数据工厂管道")

## <a name="trigger-a-pipeline"></a>触发管道

1. 从工具栏设计器图面上，选择**添加触发器** > **立即触发**。

    ![触发 Azure 数据工厂管道](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "触发 Azure 数据工厂管道")

2. 在弹出的边栏中选择“完成”。

## <a name="monitor-a-pipeline"></a>监视管道

1. 在左侧切换到“监视”选项卡。 “管道运行”列表中会显示一个管道运行。 注意“状态”列下面的运行状态。

    ![监视 Azure 数据工厂管道](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "监视 Azure 数据工厂管道")

1. 选择“刷新”可刷新状态。

1. 还可以选择“查看活动运行”图标来查看与管道关联的活动运行。 以下屏幕截图中只显示了一个活动运行，因为创建的管道中只包含一个活动。 若要切回到上一视图，请选择靠近页面顶部的“管道”。

    ![监视 Azure 数据工厂管道活动](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "监视 Azure 数据工厂管道活动")

## <a name="verify-the-output"></a>验证输出

1. 若要验证输出，请在 Azure 门户中导航到本教程使用的存储帐户。 应会看到以下文件夹或容器：

    - 一个 **adfgerstarted/outputfolder**，其中包含作为管道一部分运行的 Hive 脚本的输出。

    - 一个 **adfhdidatafactory-\<链接服务名称>-\<时间戳>** 容器。 此容器是管道运行过程中创建的 HDInsight 群集的默认存储位置。

    - 一个 **adfjobs** 容器，其中包含 Azure 数据工厂作业日志。  

        ![验证 Azure 数据工厂管道输出](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "验证 Azure 数据工厂管道输出")

## <a name="clean-up-the-tutorial"></a>结束本教程

与按需 HDInsight 群集创建，不需要显式删除 HDInsight 群集。 系统会根据创建管道时提供的配置来删除群集。 但是，即使在删除群集后，与该群集关联的存储帐户仍会继续存在。 此行为是设计使然，以便可以保留数据。 但是，如果你不希望持久保留数据，可以删除创建的存储帐户。

或者，可以删除在本教程中创建的整个资源组。 这会删除创建的存储帐户和 Azure 数据工厂。

### <a name="delete-the-resource-group"></a>删除资源组

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左窗格中选择“资源组”。
1. 选择在 PowerShell 脚本中创建的资源组名称。 如果列出了太多资源组，请使用筛选器。 这会打开资源组。
1. 除非你与其他项目共享资源组，否则在“资源”磁贴中，应列出默认存储帐户和数据工厂。
1. 选择“删除资源组”。 这样做会删除存储帐户和存储帐户中存储的数据。

    ![删除资源组](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "删除资源组")

1. 输入资源组名称以确认删除，然后选择“删除”。


## <a name="next-steps"></a>后续步骤
本文已介绍如何使用 Azure 数据工厂创建按需 HDInsight 群集和运行 [Apache Hive](https://hive.apache.org/) 作业。 转到下一步的文章，了解如何创建具有自定义配置 HDInsight 群集。

> [!div class="nextstepaction"]
>[使用自定义配置创建 Azure HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)


