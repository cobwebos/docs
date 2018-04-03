---
title: 支持 Azure 堆栈中的配置文件的资源提供程序 API 版本 |Microsoft 文档
description: 了解 Azure 堆栈中的配置文件支持的 Azure 资源管理器版本。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2E21C8DE-D540-4C1C-A0EF-1B7125DB7A6E
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 8cc5ce1ec113df7ce92c54022dbe1b6219c8c235
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>支持 Azure 堆栈中的配置文件的资源提供程序 API 版本

Azure 资源提供程序提供的资源可以部署和管理通过 Azure 资源管理器。 每个提供商提供用于处理资源的操作。 一些常见的资源提供程序包括 Microsoft.Compute，提供虚拟机、 Microsoft.Storage，提供存储帐户资源，和 Microsoft.Web，提供与 web 应用程序相关的资源。 有关详细信息，请参阅[资源提供程序和类型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)。

使用配置文件时下, 表中的每个资源提供程序指示 Azure 堆栈的 API 版本的支持的版本。

### <a name="microsoftauthorization"></a>Microsoft.Authorization

使用基于角色的访问控制来管理你组织中的用户可对资源执行的操作。 此组的操作，可定义角色、 将角色分配给用户或组，并获取有关权限的信息。 有关详细信息，请参阅[授权](https://docs.microsoft.com/rest/api/authorization/)。

| 资源类型 | API 版本 |
|---------------------|--------------------|
| 锁 | 2017-04-01 |
| 操作 | 2015-07-01 |
| 权限 | 2015-07-01 |
| 策略分配数 | 2016-12-01 （自 2017 年 1-06-01-预览版） |
| 策略定义 | 2016-12-01 |
| 提供程序操作 | 2015-07-01-preview |
| 角色分配 | 2015-07-01 |
| 角色定义 | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| 资源类型 | API 版本 |
|----------------------------------|----------------------|
| 委派的提供程序订阅 | 2015-06-01-预览 |
| 委派的使用情况聚合 | 2015-06-01-预览 |
| 估计资源花费 | 2015-06-01-预览 |
| 操作 | 2015-06-01-预览 |
| 订阅服务器使用情况聚合 | 2015-06-01-预览 |
| 使用情况聚合 | 2015-06-01-预览 |

### <a name="microsoftcompute"></a>Microsoft.Compute

Azure 计算 Api 让你以编程方式访问虚拟机和其支持的资源。 有关详细信息，请参阅[Azure 计算](https://docs.microsoft.com/en-us/rest/api/compute/)。

| 资源类型 | API 版本 |
|---------------------------------------------------------------|-------------|
| 可用性集 | 2016-03-30 |
| 位置 | 2016-03-30 |
| Locations/operations | 2016-03-30 |
| Locations/publishers | 2016-03-30 |
| Locations/usages | 2016-03-30 |
| Locations/vmSizes | 2016-03-30 |
| 操作 | 2016-03-30 |
| 虚拟机 | 2016-03-30 |
| 虚拟机/扩展 | 2016-03-30 |
| 虚拟机规模集 | 2016-03-30 |
| 虚拟机规模集/扩展 | 2016-03-30 |
| 虚拟机规模集/网络接口 | 2016-03-30 |
| 虚拟机缩放集/虚拟机 | 2016-03-30 |
| 虚拟机缩放集/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| 资源类型 | API 版本 |
|------------------|-------------|
| 特选 | 2015-04-01 |
| 特选内容 | 2015-04-01 |
| 特选摘录 | 2015-04-01 |
| 库项 | 2015-04-01 |
| 操作 | 2015-04-01 |
| 门户 | 2015-04-01 |
| 搜索 | 2015-04-01 |
| 建议 | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| 资源类型 | API 版本 |
|--------------------|--------------------|
| 预警规则 | 2016-03-01 |
| 事件类别 | 自 2017 年 1-03-01-预览 |
| 活动类型 | 自 2017 年 1-03-01-预览 |
| 指标定义 | 2016-03-01 |
| 操作 | 2015-04-01 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

管理密钥保管库以及密钥、 机密和你的密钥保管库中的证书。 有关详细信息，请参阅[Azure 密钥保管库 REST API 参考](https://docs.microsoft.com/rest/api/keyvault/)。

| 资源类型 | API 版本 |
|-------------------------|--------------|
| 操作 | 2016-10-01 |
| 保管库 | 2016-10-01 |
| 保管库 / 访问策略 | 2016-10-01 |
| Vaults/secrets | 2016-10-01 |

### <a name="microsoftkeyvaultadmin"></a>Microsoft.Keyvault.Admin

管理密钥保管库以及密钥、 机密和你的密钥保管库中的证书。 有关详细信息，请参阅[Azure 密钥保管库 REST API 参考](https://docs.microsoft.com/rest/api/keyvault/)。

| 资源类型 | API 版本 |
|------------------|--------------------|
| 位置 | 自 2017 年 1-02-01-preview |
| Locations/quotas | 自 2017 年 1-02-01-preview |

### <a name="microsoftnetwork"></a>Microsoft.Network

操作调用结果是表示的可用网络云操作列表。 有关详细信息，请参阅[操作 REST API](https://docs.microsoft.com/rest/api/operation/)。

| 资源类型 | API 版本 |
|---------------------------|--------------|
| 连接 | 2015-06-15 |
| DNS 区域 | 2016-04-01 |
| 负载均衡器 | 2015-06-15 |
| 本地网络网关 | 2015-06-15 |
| 位置 | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
| Locations/operations | 2016-04-01 |
| Locations/usages | 2016-04-01 |
| 网络接口 | 2015-06-15 |
| 网络安全组 | 2015-06-15 |
| 操作 | 2015-06-15 |
| 公共 IP 地址 | 2015-06-15 |
| 路由表 | 2015-06-15 |
| 虚拟网络网关 | 2015-06-15 |
| 虚拟网络 | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

使用 Azure 资源管理器可以部署和管理 Azure 解决方案的基础结构。 可在资源组中组织相关的资源，并使用 JSON 模板部署资源。 有关部署和管理资源与资源管理器简介，请参阅[Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

| 资源类型 | API 版本 |
|-----------------------------------------|-------------------|
| 应用程序注册 | 2015-01-01 |
| 检查资源名称 | 2015-012016-09-01 |
| 委派的提供程序 | 2015-01-01 |
| 委派的提供程序优惠 | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| 部署 | 2016-0209-01 |
| 部署/操作 | 2016-0209-01 |
| 扩展元数据 | 2015-01-01 |
| 链接 | 2015-012016-09-01 |
| 位置 | 2015-01-01 |
| 产品 | 2015-01-01 |
| 操作 | 2015-01-01 |
| 提供程序 | 2015-012017-08-01 |
| 资源组 | 2015-012016-09-01 |
| 资源 | 2015-012016-09-01 |
| 订阅 | 2015-012016-09-01 |
| Subscriptions/location | 2015-012016-09-01 |
| 每个订阅操作结果 | 2015-012016-09-01 |
| Subscriptions/providers | 2015-012017-08-01 |
| 订阅/资源组 | 2015-012016-09-01 |
| Subscriptions/resourceGroups/resources | 2015-012016-09-01 |
| Subscriptions/resources | 2015-012016-09-01 |
| Subscriptions/tagNames | 2016-0609-01 |
| Subscriptions/tagNames/tagValues | 2016-0609-01 |
| 租户 | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

存储资源提供程序 (SRP) 使您能够以编程方式管理你的存储帐户和密钥。 有关详细信息，请参阅[Azure 存储资源提供程序 REST API 参考](https://docs.microsoft.com/rest/api/storagerp/)。

| 资源类型 | API 版本 |
|-------------------------|--------------|
| 检查名称可用性 | 2016-01-01 |
| 位置 | 2016-01-01 |
| Locations/quotas | 2016-01-01 |
| 操作 | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| 用途 | 2016-01-01 |

## <a name="next-steps"></a>后续步骤

* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)  
