---
title: "Azure 配额错误 | Microsoft Docs"
description: "介绍如何解决资源配额错误。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: 6a8024a12c4a79e92e37df0a56b6e6bd0cb6a8d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-resource-quotas"></a>解决资源配额错误

本文介绍了部署资源时可能遇到的配额错误。

## <a name="symptom"></a>症状

如果部署的模板创建的资源超出 Azure 配额，则会收到如下所示的部署错误消息：

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

或者，你可能看到：

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>原因

已按资源组、订阅、帐户和其他范围应用配额。 例如，订阅可能配置为限制某个区域的核心数目。 如果尝试部署超过允许核心数目的虚拟机，则会收到指出超过配额的错误消息。
有关完整的配额信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-subscription-service-limits.md)。

## <a name="solution"></a>解决方案

### <a name="solution-1"></a>解决方案 1

对于 Azure CLI，可使用 `az vm list-usage` 命令查找虚拟机配额。

```azurecli
az vm list-usage --location "South Central US"
```

将返回：

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="solution-2"></a>解决方案 2

对于 PowerShell，可使用 Get-AzureRmVMUsage 命令查找虚拟机配额。

```powershell
Get-AzureRmVMUsage -Location "South Central US"
```

将返回：

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

### <a name="solution-3"></a>解决方案 3

如果需要提高配额限制，请前往门户并提交支持问题以增加你在要部署区域内的配额。

> [!NOTE]
> 请记住，对于资源组，配额针对每个单独的区域，而不是针对整个订阅。 如果需要在美国西部部署 30 个核心，则必须在美国西部寻求 30 个 Resource Manager 核心。 如果需要在有权访问的任何区域内部署 30 个核心，则应在所有区域内请求 30 个 Resource Manager 核心。
>
>