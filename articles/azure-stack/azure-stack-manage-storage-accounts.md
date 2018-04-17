---
title: 管理 Azure Stack 存储帐户 | Microsoft Docs
description: 了解如何查找、管理、恢复和回收 Azure Stack 存储帐户
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: anirudha
ms.openlocfilehash: ce61dec785d6207e2d2df21884525f76a6778f4a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="manage-storage-accounts-in-azure-stack"></a>管理 Azure Stack 中的存储帐户
了解如何管理 Azure Stack 中的存储帐户，以根据业务需求查找、恢复和回收存储容量。

## <a name="find"></a>查找存储帐户
可在 Azure Stack 中通过以下方式查看区域中的存储帐户列表：

1. 在 Internet 浏览器中，导航到 https://adminportal.local.azurestack.external。
2. 以云操作员身份登录到 Azure Stack 管理门户（使用部署期间提供的凭据）
3. 在默认的仪表板 – 找到**区域管理**列表，然后单击你想要探索，例如的区域**(本地**)。
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. 从“资源提供程序”列表中选择“存储”。
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. 现在，在存储资源提供程序的管理员窗格中 – 上向下滚动到**存储帐户**选项卡上，单击它。
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   结果页面显示该区域中的存储帐户列表。
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

默认显示前 10 个帐户。 可以单击列表底部的“加载更多”链接来提取更多的帐户。

或

如果你感兴趣的特定存储帐户-你可以**筛选并提取相关帐户**仅。


**筛选帐户：**

1. 单击窗格顶部的“筛选”。
2. 在“筛选”窗格中，可以指定“帐户名”、“订阅 ID”或“状态”，以调整要显示的存储帐户列表。 适当地使用筛选器。
3. 单击“更新”。 列表会相应地刷新。
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. 若要重置筛选器，请单击“筛选”、清除选择内容，然后更新。

使用搜索文本框（位于存储帐户列表窗格的顶部）可以突出显示帐户列表中选择的文本。 无法轻松获取完整名称或 ID 时，可以使用此功能。

可在此处使用任意文本，帮助找到想要查看的帐户。

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>查看帐户详细信息
找到想要查看的帐户后，可以单击特定的帐户查看特定的详细信息。 此时将打开新的窗格，其中包含帐户详细信息，例如帐户类型、创建时间、位置等等。

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>恢复已删除的帐户
有时，我们需要恢复已删除的帐户。

在 Azure Stack 中，可通过一种简单的方法实现此目的：

1. 浏览到存储帐户列表。 有关详细信息，请参阅本主题中的[查找存储帐户](#find)。
2. 在列表中找到该特定帐户。 可能需要执行筛选。
3. 检查帐户的状态。 状态应显示为“已删除”。
4. 单击该帐户打开帐户详细信息窗格。
5. 在此窗格的顶部找到“恢复”按钮，并单击它。
6. 单击“是”确认。
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. 恢复过程随即显示“正在处理...请稍候”，表示恢复成功。
   也可以单击门户顶部的铃铛图标查看进度指示。
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   成功同步已恢复的帐户之后，可以再次使用该帐户。

### <a name="some-gotchas"></a>注意事项
* 已删除的帐户显示“超出保留期”状态。
  
  超出保留期意味着已删除的帐户超出了保留期，可能无法恢复。
* 已删除的帐户未显示在帐户列表中。
  
  删除的帐户已作为垃圾回收后，该帐户可能不会显示在帐户列表中。 在此情况下，无法恢复该帐户。 请参阅本主题中的[回收容量](#reclaim)。

## <a name="set-the-retention-period"></a>设置保留期
云操作员可以使用保留期设置来指定时间间隔天数（0 到 9999 天），在此期间，任何已删除的帐户都有可能能够恢复。 默认保持期设置为 0 天。 将值设置为“0”表示任何已删除的帐户会立即超出保留期，并标记为定期进行垃圾回收。

**更改保留期：**

1. 在 Internet 浏览器中，导航到 https://adminportal.local.azurestack.external。
2. 以云操作员身份登录到 Azure Stack 管理门户（使用部署期间提供的凭据）
3. 在默认的仪表板 – 找到**区域管理**列表，然后单击你想要探索 – 例如的区域**(本地**)。
4. 从“资源提供程序”列表中选择“存储”。
5. 单击顶部的“设置”打开设置窗格。
6. 单击“配置”，然后编辑保留期值。

   设置天数并保存。
   
   此值立即生效，并且是针对整个区域设置的。

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>回收容量
使用保留期的负面影响之一是，已删除的帐户在超出保留期之前会一直消耗容量。 云操作员可能需要通过某种方式，在保留期尚未到来之前回收已删除帐户占用的空间。

可以使用门户或 PowerShell 来回收容量。

**使用门户回收容量：**
1. 导航到存储帐户窗格。 请参阅[查找存储帐户](#find)。
2. 单击窗格顶部的“回收空间”。
3. 阅读消息，并单击“确定”。

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. 等待显示成功通知（查看门户上的铃铛图标）。

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. 刷新“存储帐户”页。 已删除的帐户已被清除，因此不再显示在列表中。

也可以使用 PowerShell 显式替代保留期，并立即回收容量。

**使用 PowerShell 回收容量：**   

1. 确认已安装并配置 Azure PowerShell。 如果未安装，请遵照以下说明： 
   * 若要安装最新版本的 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
   有关 Azure 资源管理器 cmdlet 的详细信息，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](http://go.microsoft.com/fwlink/?LinkId=394767)
2. 运行以下 cmdlet：

> [!NOTE]
> 如果运行此 cmdlet，将会永久删除帐户及其内容。 不可恢复。 请慎用此 cmdlet。


        Clear-ACSStorageAccount -ResourceGroupName system.local -FarmName <farm ID>


有关详细信息，请参阅 [Azure Stack PowerShell 文档](https://msdn.microsoft.com/library/mt637964.aspx)。
 

## <a name="migrate-a-container"></a>迁移容器
由于租户的存储用量不平均，因此云操作员可能发现一个或多个基础租户共享使用的空间比其他租户要多。 如果发生这种情况，云操作员可以尝试将部分 Blob 容器手动迁移到另一个共享，为用量较大的共享释放一些空间。 

必须使用 PowerShell 迁移容器。
> [!NOTE]
>Blob 容器迁移不支持实时迁移，并且目前是脱机操作。 在迁移期间以及完成迁移之前，该容器中的基础 Blob 不可用，并且处于“脱机”状态。 

**使用 PowerShell 迁移容器：**

1. 确认已安装并配置 Azure PowerShell。 如果未安装，请遵照以下说明：
    * 若要安装最新版本的 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/)。 有关 Azure 资源管理器 cmdlet 的详细信息，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](http://go.microsoft.com/fwlink/?LinkId=394767)
2. 获取场名称： 
      
      `$farm = Get-ACSFarm -ResourceGroupName system.local`
3. 获取共享： 

   `$shares = Get-ACSShare -ResourceGroupName system.local -FarmName $farm.FarmName`

4. 获取给定共享的容器。 请注意，count 和 intent 是可选参数：
            
   `$containers = Get-ACSContainer -ResourceGroupName system.local -FarmName $farm.FarmName -ShareName $shares[0].ShareName -Count 4 -Intent Migration`  

   然后检查 $containers：

   `$containers`

    ![](media/azure-stack-manage-storage-accounts/image13.png)
5. 获使用于容器迁移的最佳目标共享：

    `$destinationshares= Get-ACSSharesForMigration  -ResourceGroupName system.local -FarmName $farm.farmname -SourceShareName $shares[0].ShareName`

    然后检查 $destinationshares：

    `$destinationshares`

    ![](media/azure-stack-manage-storage-accounts/image14.png)
6. 开始进行容器迁移。请注意，这是一个异步实现，因此可以循环共享中的所有容器，并使用返回的作业 ID 来跟踪状态。

    `$jobId = Start-ACSContainerMigration -ResourceGroupName system.local -FarmName $farm.farmname -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares.UncPath`

    然后检查 $jobId：

   ```
   $jobId
   d1d5277f-6b8d-4923-9db3-8bb00fa61b65
   ```
7. 根据作业 ID 检查迁移作业的状态。 容器迁移完成后，MigrationStatus 会设置为“Completed”。

    `Get-ACSContainerMigrationStatus -ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image15.png)

8. 可以取消正在进行的迁移作业。 这同样是一个异步操作，可以使用 $jobid 来跟踪其状态：

    `Stop-ACSContainerMigration-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId-Verbose`

    ![](media/azure-stack-manage-storage-accounts/image16.png)

    可以再次检查迁移取消状态：

    `Get-ACSContainerMigrationStatus-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image17.png)




  
  
