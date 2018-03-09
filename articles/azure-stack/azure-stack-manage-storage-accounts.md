---
title: "管理 Azure 堆栈存储帐户 |Microsoft 文档"
description: "了解如何查找、 管理、 恢复和回收 Azure 堆栈存储帐户"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: anirudha
ms.openlocfilehash: 395cd113e21bf747c796ff28026f552f30656b47
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="manage-storage-accounts-in-azure-stack"></a>管理 Azure 堆栈中的存储帐户
了解如何管理 Azure 堆栈以查找，恢复，并回收根据业务需要的存储容量中的存储帐户。

## <a name="find"></a>查找存储帐户
可以通过 Azure 堆栈中查看区域中的存储帐户的列表：

1. 在 Internet 浏览器，导航到 https://adminportal.local.azurestack.external。
2. 为云运算符 （使用你在部署过程中提供的凭据） 登录到 Azure 堆栈管理门户
3. 在默认的仪表板 – 找到**区域管理**列表，然后单击你想要探索，例如的区域**(本地**)。
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. 选择**存储**从**资源提供程序**列表。
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. 现在，在存储资源提供程序的管理员窗格中 – 上向下滚动到**存储帐户**选项卡上，单击它。
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   生成页是该区域中的存储帐户的列表。
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

默认情况下，显示前 10 个帐户。 你可以选择提取的详细信息通过单击**加载其他**列表底部的链接。

或

如果你感兴趣的特定存储帐户-你可以**筛选并提取相关帐户**仅。


**若要筛选的帐户：**

1. 单击**筛选器**窗格的顶部。
2. 在筛选器窗格中，它允许你指定**帐户名称**，* * 订阅 ID，或**状态**微调的存储帐户，要显示的列表。 根据需要将它们。
3. 单击“更新”。 应相应地刷新列表。
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. 若要重置筛选器： 单击**筛选器**、 清除所做的选择和更新。

（顶部存储帐户列表窗格中） 的搜索文本框中，可以突出显示的帐户列表中选定的文本。 如果完整名称或 ID 不是易于使用，则可以使用此工具。

你可以使用此处免费文本来帮助查找你感兴趣的帐户。

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>查看帐户的详细信息
一旦找到你感兴趣查看的帐户，你可以单击要查看某些详细信息的特定帐户。 新窗格将打开，其中的帐户详细信息如： 帐户、 创建时间、 位置等的类型。

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>恢复已删除的帐户
你可能需要恢复已删除的帐户的情况下。

在 Azure 堆栈是一种简单的方法来做到这一点：

1. 浏览到存储帐户列表。 请参阅[查找存储帐户](#find)有关详细信息的本主题中。
2. 列表中找到该特定帐户。 你可能需要进行筛选。
3. 检查*状态*的帐户。 它应该显示**已删除**。
4. 单击打开帐户详细信息窗格的帐户。
5. 基于此窗格中，找到**恢复**按钮，然后单击它。
6. 单击“是”确认。
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. 恢复为现在位于*...处理等待*表明是否成功。
   您也可以单击顶部的门户以查看进度指示"钟形"图标。
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   已成功同步后已恢复的帐户，可以再次使用。

### <a name="some-gotchas"></a>某些问题
* 你已删除的帐户将显示状态作为**外保留**。
  
  超出保留方式已删除的帐户已超出的保持期，可能不是可恢复。
* 你已删除的帐户不显示帐户列表中。
  
  在已删除的帐户已进行了垃圾收集您的帐户可能不会显示帐户列表。 在这种情况下，无法恢复它。 请参阅[回收容量](#reclaim)本主题中。

## <a name="set-the-retention-period"></a>设置保持期
保持期设置允许云运算符来指定在其间可能可以恢复已删除的任何帐户 （介于 0 和 9999 天） 之间的天内的时间段。 默认保持期设置为 15 天。 将该值设置为"0"表示任何已删除的帐户立即不保留，并且标记为定期垃圾回收。

**若要更改的保持期：**

1. 在 internet 浏览器，导航到 https://adminportal.local.azurestack.external。
2. 为云运算符 （使用你在部署过程中提供的凭据） 登录到 Azure 堆栈管理门户
3. 在默认的仪表板 – 找到**区域管理**列表，然后单击你想要探索 – 例如的区域**(本地**)。
4. 选择**存储**从**资源提供程序**列表。
5. 单击**设置**顶部以打开设置窗格。
6. 单击**配置**然后编辑保持期值。

   设置的天数，然后保存它。
   
   此值是立即生效，为你整个区域设置。

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>回收容量
具有保留期的负面影响之一是已删除的帐户继续使用容量，直到注销的保持期。 作为云操作员可能需要一种方法以回收已删除的帐户空间，即使尚未过期的保持期。

你可以回收容量使用门户或 PowerShell。

**若要回收容量使用门户：**
1. 导航到存储帐户窗格。 请参阅[查找存储帐户](#find)。
2. 单击**回收空间**窗格的顶部。
3. 读取消息，然后单击**确定**。

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. 等待成功通知钟形图标在门户上，请参阅。

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. 刷新存储帐户页。 在列表中不再显示已删除的帐户，因为已经清除。

你可以还使用 PowerShell 来显式重写的保留期并立即回收容量。

**若要回收容量使用 PowerShell:**   

1. 确认你有 Azure PowerShell 安装和配置。 如果没有，请按照以下说明： 
   * 若要安装最新的 Azure PowerShell 版本，并将其与你的 Azure 订阅关联，请参阅[如何安装和配置 Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
   有关 Azure 资源管理器 cmdlet 详细信息，请参阅[使用 Azure PowerShell 与 Azure 资源管理器](http://go.microsoft.com/fwlink/?LinkId=394767)
2. 运行以下 cmdlet：

> [!NOTE]
> 如果你运行此 cmdlet，永久删除的帐户，而且其内容。 不可恢复。 使用此方法时要小心。


        Clear-ACSStorageAccount -ResourceGroupName system.local -FarmName <farm ID>


有关详细信息，请参阅[Azure 堆栈 powershell 文档。](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="migrate-a-container"></a>迁移容器
由于租户不均衡的存储使用，云操作员可能会发现一个，或使用更多的空间比其他更基础租户共享。 如果发生这种情况，云操作员可以尝试通过手动将某些 blob 容器迁移到另一个共享释放重音共享上的一些空间。 

你必须使用 PowerShell 迁移容器。
> [!NOTE]
>Blob 容器迁移不支持实时迁移和当前是脱机操作。 在迁移期间和完成之前不能使用该容器中的基础 blob，并且"脱机"。 

**若要迁移使用 PowerShell 的容器：**

1. 确认你有 Azure PowerShell 安装和配置。 如果没有，请按照以下说明：
    * 若要安装最新的 Azure PowerShell 版本，并将其与你的 Azure 订阅关联，请参阅[如何安装和配置 Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/)。 有关 Azure 资源管理器 cmdlet 详细信息，请参阅[使用 Azure PowerShell 与 Azure 资源管理器](http://go.microsoft.com/fwlink/?LinkId=394767)
2. 获取服务器场的名称： 
      
      `$farm = Get-ACSFarm -ResourceGroupName system.local`
3. 获取共享： 

   `$shares = Get-ACSShare -ResourceGroupName system.local -FarmName $farm.FarmName`

4. 获取给定共享容器。 请注意计数和意图是可选参数：
            
   `$containers = Get-ACSContainer -ResourceGroupName system.local -FarmName $farm.FarmName -ShareName $shares[0].ShareName -Count 4 -Intent Migration`  

   然后检查 $containers:

   `$containers`

    ![](media/azure-stack-manage-storage-accounts/image13.png)
5. 获取容器迁移最佳的目标共享：

    `$destinationshares= Get-ACSSharesForMigration  -ResourceGroupName system.local -FarmName $farm.farmname -SourceShareName $shares[0].ShareName`

    然后检查 $destinationshares:

    `$destinationshares`

    ![](media/azure-stack-manage-storage-accounts/image14.png)
6. 启动容器的迁移，请注意这是异步的实现，因此一个可以循环共享中的所有容器，跟踪状态使用返回的作业 id。

    `$jobId = Start-ACSContainerMigration -ResourceGroupName system.local -FarmName $farm.farmname -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares.UncPath`

    然后检查 $jobId:

   ```
   $jobId
   d1d5277f-6b8d-4923-9db3-8bb00fa61b65
   ```
7. 检查作业 id 的迁移作业的状态 当容器迁移完成后时，将 MigrationStatus 设置为"已完成。"

    `Get-ACSContainerMigrationStatus -ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image15.png)

8. 你可以取消正在进行的迁移作业。 再次，这是一个异步操作，并可以使用 $jobid 跟踪：

    `Stop-ACSContainerMigration-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId-Verbose`

    ![](media/azure-stack-manage-storage-accounts/image16.png)

    你可以检查再次取消迁移的状态：

    `Get-ACSContainerMigrationStatus-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image17.png)




  
  