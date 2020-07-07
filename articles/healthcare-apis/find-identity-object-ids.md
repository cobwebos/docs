---
title: 查找用于身份验证的标识对象 ID - Azure API for FHIR
description: 本文介绍如何查找为 Azure API for FHIR 配置身份验证所需的标识对象 ID
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 88c0349ce220229920a39ba4cb6640f3d4dde93b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871197"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>查找用于身份验证配置的标识对象 ID

在本文中，你将了解如何查找将 Azure API for FHIR 配置为[使用外部或辅助 Active Directory 租户](configure-local-rbac.md)作为数据平面时所需的标识对象 id。

## <a name="find-user-object-id"></a>查找用户对象 ID

如果用户使用的是用户名 `myuser@consoso.com` ，则可以 `ObjectId` 使用以下 PowerShell 命令查找用户：

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

或者，您可以使用 Azure CLI：

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

## <a name="find-service-principal-object-id"></a>查找服务主体对象 ID

假设你已注册了[服务客户端应用](register-service-azure-ad-client-app.md)，并且希望允许此服务客户端访问用于 FHIR 的 Azure API，则可以使用以下 PowerShell 命令查找客户端服务主体的对象 ID：

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

其中 `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` ，是服务客户端应用程序 ID。 或者，你可以使用 `DisplayName` 服务客户端的：

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

如果使用的是 Azure CLI，则可以使用：

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | jq -r .objectId
```

## <a name="find-a-security-group-object-id"></a>查找安全组对象 ID

如果要查找安全组的对象 ID，可以使用以下 PowerShell 命令：

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
其中 `mygroup` ，是你感兴趣的组的名称。

如果使用的是 Azure CLI，则可以使用：

```azurecli-interactive
az ad group show --group "mygroup" | jq -r .objectId
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何查找将 Azure API for FHIR 配置为使用外部或辅助 Azure Active Directory 租户所需的标识对象 Id。 接下来，了解如何使用对象 Id 配置本地 RBAC 设置：
 
>[!div class="nextstepaction"]
>[配置本地 RBAC 设置](configure-local-rbac.md)