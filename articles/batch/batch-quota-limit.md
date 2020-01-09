---
title: 服务配额和限制 - Azure Batch | Microsoft Docs
description: 了解默认的 Azure Batch 配额、限制和约束，以及如何请求提高配额
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: b63032baa60d18f3d9d98fc403bdc1087c6c9b6b
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660675"
---
# <a name="batch-service-quotas-and-limits"></a>Batch 服务配额和限制

与其他 Azure 服务一样，与 Batch 服务关联的某些资源存在限制。 其中的许多限制是 Azure 在订阅或帐户级别应用的默认配额。 本文将描述这些默认值，以及如何请求提高配额。

设计和增加 Batch 工作负荷时，请记住这些配额。 例如，如果池没有达到指定的计算节点目标数量，那么可能是已达到 Batch 帐户的核心配额限制。

可以在单个批处理帐户中运行多个批处理工作负荷，或者在相同订阅的不同 Azure 区域的批处理帐户之间分散工作负荷。

如果打算在 Batch 中运行生产工作负荷，可能需要将一个或多个配额提高到默认值以上。 如果需要提高配额，可以免费提出在线[客户支持请求](#increase-a-quota)。

## <a name="resource-quotas"></a>资源配额

配额是一种信用限制，不附带容量保证。 如果有大规模的容量需求，请联系 Azure 支持。

另请注意，配额不能保证值。 配额可能因批处理服务的更改或用户请求更改配额值而异。

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>用户订阅模式中的核心配额

如果创建了 Batch 帐户，并将池分配模式设置为“用户订阅”，则会以不同的方式应用配额。 在此模式下，会在创建池后直接在订阅中创建 Batch VM 和其他资源。 Azure Batch 核心配额不会应用到在此模式下创建的帐户。 对于此类帐户，将应用订阅中的区域计算核心数和其他资源的配额。 在 [Azure 订阅和服务的限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)中详细了解这些配额。

## <a name="pool-size-limits"></a>池大小限制

池大小限制由 Batch 服务设置。 与[资源配额](#resource-quotas)不同，这些值不能更改。 只有在节点间通信和自定义映像的池有不同于标准配额的限制。

| **资源** | **最大限制** |
| --- | --- |
| **[启用了节点间通信的池](batch-mpi.md)中的计算节点**  ||
| Batch 服务池分配模式 | 100 |
| Batch 订阅池分配模式 | 80 |
| **[使用托管映像资源1创建的池中](batch-custom-images.md)的计算节点**<sup></sup> ||
| 专用节点 | 2000 |
| 低优先级节点 | 1000 |

<sup>1</sup> 适用于未启用节点间通信的池。

## <a name="other-limits"></a>其他限制

Batch 服务设置的其他限制。 与[资源配额](#resource-quotas)不同，这些值不能更改。

| **资源** | **最大限制** |
| --- | --- |
| 每个计算节点的[并发任务](batch-parallel-node-tasks.md)数 | 4 x 节点核心数 |
| 每个 Batch 帐户的[应用程序](batch-application-packages.md)数 | 20 |
| 每个应用程序的应用程序包数 | 40 |
| 每个池的应用程序包 | 10 |
| 最长任务生存期 | 180 天<sup>1</sup> |
| 每个计算节点的[装载](virtual-file-mount.md) | 10 |

<sup>1</sup> 最长任务生存期（从添加到作业时算起到任务完成时结束）为 180 天。 已完成的任务保留7天;不能在最大生存期内完成的任务数据不可访问。

## <a name="view-batch-quotas"></a>查看 Batch 配额

在[Azure 门户][portal]中查看 Batch 帐户配额。

1. 在门户中选择“Batch 帐户”，并选择所需的 Batch 帐户。
1. 在 Batch 帐户的菜单上选择“配额”。
1. 显示当前应用于 Batch 帐户的配额

    ![Batch 帐户配额][account_quotas]

## <a name="increase-a-quota"></a>提高配额

请按照以下步骤使用[Azure 门户][portal]来请求批处理帐户或订阅的配额增加。 可以提高哪种配额取决于批处理帐户的池分配模式。 若要请求增加配额，必须包含要增加其配额的 VM 系列。 当应用了配额增加时，它将应用于所有系列的 Vm。

### <a name="increase-cores-quota-in-batch"></a>在 Batch 中增加内核配额 

1. 在门户仪表板上选择“帮助 + 支持”磁贴，或单击门户右上角的问号 ( **?** )。
1. 选择“新建支持请求” > “基本”。
1. 在“基本信息”中：
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 **问题类型** > **服务和订阅限制（配额）**
   
    b.保留“数据库类型”设置，即设置为“共享”。 选择订阅。
   
    c. “配额类型” > “Batch”
      
    选择“**下一页**”。
    
1. 在“详细信息”中：
      
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 "**提供详细信息**" 中，指定位置、配额类型和批处理帐户。
    
    ![批处理配额增加][quota_increase]

    配额类型包括：

    * **每个批处理帐户**  
        特定于单个批处理帐户的值，包括专用和低优先级内核以及作业和池的数量。
        
    * **每个区域**  
        适用于区域中所有批处理帐户的值，包括每个订阅每个区域的批处理帐户数。

    低优先级配额是跨所有 VM 系列的单个值。 如果需要受约束的 Sku，则必须选择**低优先级内核**，并包括要请求的 VM 系列。

    b.保留“数据库类型”设置，即设置为“共享”。 根据[业务影响][support_sev]选择**严重性**。

    选择“**下一页**”。

1. 在“联系人信息”中：
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“首选联系方法”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 输入并确认所需的联系人详细信息。
   
    选择 "**创建**" 提交支持请求。

提交支持请求后，Azure 支持人员将与你取得联系。 可以在几分钟内完成配额请求，或在最多两个工作日内完成。

## <a name="related-quotas-for-vm-pools"></a>VM 池的相关配额

部署在 Azure 虚拟网络中的虚拟机配置中的 Batch 池可自动分配其他 Azure 网络资源。 在虚拟网络中，每 50 个池节点需要以下资源：

* 一个[网络安全组](../virtual-network/security-overview.md#network-security-groups)
* 一个[公共 IP 地址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* 一个[负载均衡器](../load-balancer/load-balancer-overview.md)

在包含创建 Batch 池时提供的虚拟网络的订阅中分配这些资源。 这些资源受订阅的[资源配额](../azure-resource-manager/management/azure-subscription-service-limits.md)限制。 如果计划在虚拟网络中部署大型池，请检查订阅的这些资源配额。 如果需要，请在 Azure 门户中选择“帮助和支持”，请求增大配额。


## <a name="related-topics"></a>相关主题
* [使用 Azure 门户创建 Azure Batch 帐户](batch-account-create-portal.md)
* [Azure Batch 功能概述](batch-api-basics.md)
* [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
