---
title: Azure 资源提供程序注册错误 | Microsoft Docs
description: 说明如何解决 Azure 资源提供程序注册错误。
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
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: b90009c1cd08a1004e58c4b9f25cd6350712fbcd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-errors-for-resource-provider-registration"></a>解决资源提供程序注册的错误

本文介绍使用之前未在订阅中使用过的资源提供程序时可能遇到的错误。

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

## <a name="cause"></a>原因

可能由于下三种原因之一而收到此错误：

1. 尚未为订阅注册资源提供程序
1. 资源类型不支持该 API 版本
1. 资源类型不支持该位置

## <a name="solution-1---powershell"></a>解决方案 1 - PowerShell

对于 PowerShell，请使用 Get-AzureRmResourceProvider 查看注册状态。

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

若要注册提供程序，请使用 **Register-AzureRmResourceProvider**，并提供想要注册的资源提供程序的名称。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

若要获取特定类型的资源支持的位置，请使用：

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

若要获取特定类型的资源支持的 API 版本，请使用：

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
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

1. 对于订阅，选择“资源提供程序”。

   ![选择资源提供程序](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. 查看资源提供程序的列表，根据需要选择“注册”链接，注册尝试部署的类型的资源提供程序。

   ![列出资源提供程序](./media/resource-manager-register-provider-errors/list-resource-providers.png)
