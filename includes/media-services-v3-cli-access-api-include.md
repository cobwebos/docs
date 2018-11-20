---
title: include 文件
description: include 文件
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/11/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616633"
---
## <a name="access-the-media-services-api"></a>访问媒体服务 API

若要连接到 Azure 媒体服务 API，请使用 Azure AD 服务主体身份验证。 以下命令创建 Azure AD 应用程序并将服务主体附加到帐户。 应使用返回的值配置应用程序。

在运行脚本之前，可以将 `amsaccount` 和 `amsResourceGroup` 替换为在创建这些资源时选择的名称。 `amsaccount` 是要向其附加服务主体的 Azure 媒体服务帐户的名称。

以下命令返回 `json` 输出：

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

此命令会生成如下响应：

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

如果想要在响应中获得 `xml`，请使用以下命令：

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
