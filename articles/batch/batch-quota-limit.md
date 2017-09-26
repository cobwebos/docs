---
title: "Azure Batch 服务配额和限制 | Microsoft Docs"
description: "了解默认的 Azure Batch 配额、限制和约束，以及如何请求提高配额"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: f3f69ed8d3a985afe07e648e7512a88b25278ced
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017

---
# <a name="batch-service-quotas-and-limits"></a>Batch 服务配额和限制

与其他 Azure 服务一样，与 Batch 服务关联的某些资源存在限制。 其中的许多限制是 Azure 在订阅或帐户级别应用的默认配额。 本文将描述这些默认值，以及如何请求提高配额。

设计和增加 Batch 工作负荷时，请记住这些配额。 例如，如果池没有达到指定的计算节点目标数量，那么可能是已达到批处理帐户的核心配额限制，或者已达到订阅的区域 VM 核心配额。

可以在单个批处理帐户中运行多个批处理工作负荷，或者在相同订阅的不同 Azure 区域的批处理帐户之间分散工作负荷。

如果你打算在 Batch 中运行生产工作负荷，可能需要将一个或多个配额提高到默认值以上。 如果需要提高配额，可以免费提出在线[客户支持请求](#increase-a-quota)。

> [!NOTE]
> 配额是一种信用限制，不附带容量保证。 如果你有大规模的容量需求，请联系 Azure 支持。
> 
> 

## <a name="resource-quotas"></a>资源配额
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="quotas-in-user-subscription-mode"></a>用户订阅模式中的配额

对于池分配模式设置为**用户订阅**的批处理帐户，将在创建池时，在订阅中直接创建批处理 VM 和其他资源（例如存储帐户）。 Azure Batch 核心配额不会应用到在此模式下创建的帐户。 对于此类帐户，将应用订阅中的区域计算核心数和其他资源的配额。 在 [Azure 订阅和服务的限制、配额和约束](../azure-subscription-service-limits.md)中详细了解这些配额。

针对在用户订阅模式下创建的帐户规划资源用量时，请注意，每 40 个 Linux VM 或每 20 个 Windows VM 需要以下批处理资源（此外，还需要计算核心）：

| 资源 | 配额 | 提供程序 |
| --- | ---| --- |
| 一个存储帐户 | 存储帐户 | Microsoft.Storage |
| 一个公共 IP 地址 | 公共 IP 地址 | Microsoft.Network | 
| 一个虚拟网络 | 虚拟网络 | Microsoft.Network | 
| 一个网络安全组 | 网络安全组 | Microsoft.Network | 
| 一个虚拟机规模集 | 虚拟机规模集 | Microsoft.Compute | 
| 一个负载均衡器 | 负载均衡器 | Microsoft.Network | 

应该根据一个或多个批处理池所需的 VM 大小设置区域级别的或每个 VM 系列的核心配额：

| 配额 | 提供程序 |
| --- | ---- |
| 区域核心总数 | Microsoft.Compute |
| … 系列核心数 | Microsoft.Compute |



## <a name="other-limits"></a>其他限制
| **资源** | **最大限制** |
| --- | --- |
| 每个计算节点的[并发任务](batch-parallel-node-tasks.md)数 |4 x 节点核心数 |
| 每个 Batch 帐户的[应用程序](batch-application-packages.md)数 |20 |
| 每个应用程序的应用程序包数 |40 |
| 应用程序包大小（每个） |约 195GB<sup>1</sup> |
| 启动任务最大大小 | 32,768 个字符<sup>2</sup> |

<sup>1</sup> 最大的块 Blob 大小的 Azure 存储限制<br />
<sup>2</sup> 包括资源文件和环境变量

## <a name="view-batch-quotas"></a>查看 Batch 配额
可在 [Azure 门户][portal]中查看批处理帐户配额。

1. 在门户中选择“Batch 帐户”，然后选择所需的 Batch 帐户。
2. 在批处理帐户的菜单边栏选项卡中选择“属性”。
3. “属性”边栏选项卡显示了当前应用于 Batch 帐户的**配额**
   
    ![Batch 帐户配额][account_quotas]

对于在用户订阅模式下创建的批处理帐户，可在 Azure 门户中查看相关的订阅配额。

1. 选择“订阅”，然后选择用于批处理帐户的订阅。

2. 在“订阅”边栏选项卡中，选择“用量 + 配额”。



## <a name="increase-a-quota"></a>提高配额
执行以下步骤，使用 [Azure 门户][portal]请求提高批处理帐户或订阅的配额。 可以提高哪种配额取决于批处理帐户的池分配模式。

### <a name="increase-a-batch-cores-quota"></a>提高批处理核心配额 

如果批处理帐户是在**批处理服务**模式下创建的，请执行以下步骤请求提高批处理核心配额：

1. 在门户仪表板上选择“帮助 + 支持”磁贴，或单击门户右上角的问号 (**?**)。
2. 选择“新建支持请求” > “基本”。
3. 在“基本”边栏选项卡上：
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 “问题类型” > “配额”
   
    b.保留“数据库类型”设置，即设置为“共享”。 选择你的订阅。
   
    c. “配额类型” > “Batch”
   
    d.单击“下一步”。 “支持计划” > “配额支持 - 已包括”
   
    单击“下一步”。
4. 在“问题”边栏选项卡上：
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 根据[业务影响情况][support_sev]选择“严重性”。
   
    b. 在“详细信息”中，指定想要更改的每个配额、Batch 帐户名和新限制。
   
    单击“下一步”。
5. 在“联系信息”边栏选项卡上：
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“首选联系方法”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 输入并确认所需的联系人详细信息。
   
    单击“创建”提交支持请求。

提交支持请求后，Azure 支持人员将与你取得联系。 请注意，完成该请求最多需要 2 个工作日。

### <a name="increase-a-subscription-cores-quota"></a>提高订阅核心配额

如果批处理帐户是在**用户订阅**模式下创建的，而你需要更多的区域核心或 VM 系列核心，可以请求提高订阅中的配额。 有关步骤，请参阅 [Resource Manager 核心配额增加请求](../azure-supportability/resource-manager-core-quotas-request.md)。



## <a name="related-topics"></a>相关主题
* [使用 Azure 门户创建 Azure Batch 帐户](batch-account-create-portal.md)
* [Azure Batch 功能概述](batch-api-basics.md)
* [Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

