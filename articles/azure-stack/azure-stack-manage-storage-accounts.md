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
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 2ae2b628b2e61893a5289151c3b405e7412e7d13
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="manage-storage-accounts-in-azure-stack"></a>管理 Azure Stack 中的存储帐户
了解如何管理 Azure Stack 中的存储帐户，以根据业务需求查找、恢复和回收存储容量。

## <a name="find"></a>查找存储帐户
可在 Azure Stack 中通过以下方式查看区域中的存储帐户列表：

1. 在 Internet 浏览器中，导航到 https://adminportal.local.azurestack.external。
2. 以云操作员身份登录到 Azure Stack 管理门户（使用部署期间提供的凭据）
3. 在默认的仪表板 – 找到**区域管理**列表，然后选择你想要探索，例如的区域 **(本地**)。
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. 从“资源提供程序”列表中选择“存储”。
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. 现在，在存储资源提供程序的管理员窗格中 – 上向下滚动到**存储帐户**选项卡上，然后选择它。
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   结果页面显示该区域中的存储帐户列表。
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

默认显示前 10 个帐户。 可以单击列表底部的“加载更多”链接来提取更多的帐户。

或

如果你感兴趣的特定存储帐户-你可以**筛选并提取相关帐户**仅。


**筛选帐户：**

1. 选择**筛选器**窗格的顶部。
2. 在“筛选”窗格中，可以指定“帐户名”、“订阅 ID”或“状态”，以调整要显示的存储帐户列表。 适当地使用筛选器。
3. 选择“更新”。 列表会相应地刷新。
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. 若要重置筛选器： 选择**筛选器**、 清除所做的选择和更新。

使用搜索文本框（位于存储帐户列表窗格的顶部）可以突出显示帐户列表中选择的文本。 无法轻松获取完整名称或 ID 时，可以使用此功能。

可在此处使用任意文本，帮助找到想要查看的帐户。

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>查看帐户详细信息
一旦找到你感兴趣查看的帐户，你可以选择要查看某些详细信息的特定帐户。 此时将打开新的窗格，其中包含帐户详细信息，例如帐户类型、创建时间、位置等等。

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>恢复已删除的帐户
有时，我们需要恢复已删除的帐户。

在 Azure Stack 中，可通过一种简单的方法实现此目的：

1. 浏览到存储帐户列表。 有关详细信息，请参阅本主题中的[查找存储帐户](#find)。
2. 在列表中找到该特定帐户。 可能需要执行筛选。
3. 检查帐户的状态。 状态应显示为“已删除”。
4. 选择的帐户，这将打开帐户详细信息窗格。
5. 基于此窗格中，找到**恢复**按钮，然后选择它。
6. 请选择“是”以确认。
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. 恢复过程随即显示“正在处理...请稍候”，表示恢复成功。
   你还可以选择在门户中查看进度指示顶部的"钟形"图标。
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   成功同步已恢复的帐户之后，可以再次使用该帐户。

### <a name="some-gotchas"></a>注意事项
* 已删除的帐户显示“超出保留期”状态。
  
  超出保留期意味着已删除的帐户超出了保留期，可能无法恢复。
* 已删除的帐户未显示在帐户列表中。
  
  删除的帐户已作为垃圾回收后，该帐户可能不会显示在帐户列表中。 在此情况下，无法恢复该帐户。 请参阅本主题中的[回收容量](#reclaim)。

## <a name="set-the-retention-period"></a>设置保留期
云操作员可以使用保留期设置来指定时间间隔天数（0 到 9999 天），在此期间，任何已删除的帐户都有可能能够恢复。 默认保留期设置为 0 天。 将值设置为“0”表示任何已删除的帐户会立即超出保留期，并标记为定期进行垃圾回收。

**更改保留期：**

1. 在 Internet 浏览器中，导航到 https://adminportal.local.azurestack.external。
2. 以云操作员身份登录到 Azure Stack 管理门户（使用部署期间提供的凭据）
3. 在默认的仪表板 – 找到**区域管理**列表，然后选择你想要探索 – 例如的区域 **(本地**)。
4. 从“资源提供程序”列表中选择“存储”。
5. 选择**设置**顶部以打开设置窗格。
6. 选择**配置**然后编辑保持期值。

   设置天数并保存。
   
   此值立即生效，并且是针对整个区域设置的。

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>回收容量
使用保留期的负面影响之一是，已删除的帐户在超出保留期之前会一直消耗容量。 云操作员可能需要通过某种方式，在保留期尚未到来之前回收已删除帐户占用的空间。

可以使用门户或 PowerShell 来回收容量。

**使用门户回收容量：**
1. 导航到存储帐户窗格。 请参阅[查找存储帐户](#find)。
2. 选择**回收空间**窗格的顶部。
3. 读取消息，然后选择**确定**。

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
> 如果你运行这些 cmdlet，永久删除的帐户，而且其内容。 不可恢复。 请慎用此 cmdlet。

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
````

有关详细信息，请参阅[Azure 堆栈 PowerShell 文档。](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="next-steps"></a>后续步骤

 - 有关管理权限的信息请参阅[Manage Role-Based 访问控制](azure-stack-manage-permissions.md)。
 - 有关管理的 Azure 堆栈的存储容量的信息，请参阅[管理 Azure 堆栈的存储容量](azure-stack-manage-storage-shares.md)。