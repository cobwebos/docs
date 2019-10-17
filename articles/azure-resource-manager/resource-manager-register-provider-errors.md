---
title: Azure 资源提供程序注册错误 | Microsoft Docs
description: 介绍如何在 Azure 资源管理器中部署资源时解决 Azure 资源提供程序注册错误。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: fcdcfdfe736f29f18ea2dc240a66fd7fa6bc404b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390262"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>解决资源提供程序注册的错误

本文介绍使用之前未在订阅中使用过的资源提供程序时可能遇到的错误。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>症状

部署资源时，可能会收到以下错误代码和消息：

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

或者，可能收到类似的消息，指出：

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

错误消息应提供有关支持的位置和 API 版本的建议。 可以将模板更改为建议的值之一。 Azure 门户或正在使用的命令行接口会自动注册大多数提供程序；但非全部。 如果以前未使用特定的资源提供程序，则可能需要注册该提供程序。

或者，禁用虚拟机的自动关闭功能时，可能会收到类似如下的错误消息：

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>原因

可能会由于以下原因之一而收到这些错误：

* 尚未为订阅注册所需的资源提供程序
* 资源类型不支持该 API 版本
* 资源类型不支持该位置
* 对于 VM 的自动关闭，必须注册 Microsoft.DevTestLab 资源提供程序。

## <a name="solution-1---powershell"></a>解决方案 1 - PowerShell

对于 PowerShell，请使用 Get-AzResourceProvider 查看注册状态。

```powershell
Get-AzResourceProvider -ListAvailable
```

若要注册提供程序，请使用 **Register-AzResourceProvider**，并提供想要注册的资源提供程序的名称。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

若要获取特定类型的资源支持的位置，请使用：

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

若要获取特定类型的资源支持的 API 版本，请使用：

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>解决方案 2 - Azure CLI

若要查看是否已注册提供程序，请使用 `az provider list` 命令。

```azurecli-interactive
az provider list
```

若要注册资源提供程序，请使用 `az provider register` 命令，并指定要注册的*命名空间*。

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

若要查看支持用于某个资源类型的位置和 API 版本，请使用：

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>解决方案 3 - Azure 门户

可以通过门户查看注册状态，并注册资源提供程序命名空间。

1. 在门户中，选择“所有服务”。

   ![选择所有服务](./media/resource-manager-register-provider-errors/select-all-services.png)

1. 选择 **订阅**。

   ![选择订阅](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. 从订阅列表中，选择要用于注册资源提供程序的订阅。

   ![选择订阅以注册资源提供程序](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. 对于订阅，选择“资源提供程序”。

   ![选择资源提供程序](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. 查看资源提供程序列表，根据需要选择“注册”链接，注册尝试部署的类型的资源提供程序。

   ![列出资源提供程序](./media/resource-manager-register-provider-errors/list-resource-providers.png)
