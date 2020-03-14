---
title: 配额错误
description: 介绍如何在 Azure 资源管理器中部署资源时解决资源配额错误。
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 410b086b39d63d03491d390364f4aec6300fc7c1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273782"
---
# <a name="resolve-errors-for-resource-quotas"></a>解决资源配额错误

本文介绍了部署资源时可能遇到的配额错误。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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
有关完整的配额信息，请参阅 [Azure 订阅和服务限制、配额与约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

## <a name="troubleshooting"></a>故障排除

### <a name="azure-cli"></a>Azure CLI

对于 Azure CLI，可使用 `az vm list-usage` 命令查找虚拟机配额。

```azurecli
az vm list-usage --location "South Central US"
```

将返回：

```output
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

### <a name="powershell"></a>PowerShell

对于 PowerShell，可使用 Get-AzVMUsage 命令查找虚拟机配额。

```powershell
Get-AzVMUsage -Location "South Central US"
```

将返回：

```output
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>解决方案

若要请求增加配额，请转到门户并提出支持问题。 在支持问题中，为你想要在其中进行部署的区域请求增加配额。

> [!NOTE]
> 请记住，对于资源组，配额针对每个单独的区域，而不是针对整个订阅。 如果需要在美国西部部署 30 个核心，则必须在美国西部寻求 30 个 Resource Manager 核心。 如果需要在有权访问的任何区域内部署 30 个核心，则应在所有区域内请求 30 个 Resource Manager 核心。
>
>

1. 选择 **订阅**。

   ![Subscriptions](./media/error-resource-quota/subscriptions.png)

2. 选择需要增加配额的订阅。

   ![选择订阅](./media/error-resource-quota/select-subscription.png)

3. 选择“使用情况 + 配额”

   ![选择使用情况和配额](./media/error-resource-quota/select-usage-quotas.png)

4. 在右上角选择“请求增加”。

   ![请求增加](./media/error-resource-quota/request-increase.png)

5. 填写你需要增加的配额类型的表单。

   ![填写表单](./media/error-resource-quota/forms.png)