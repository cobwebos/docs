---
title: 快速入门：使用 PowerShell 部署 Azure API for FHIR
description: 本快速入门介绍如何使用 PowerShell 部署 Azure API for FHIR。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: matjazl
ms.openlocfilehash: 4b2772b449b3c398c8c8932db58b7078b7501824
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851964"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>快速入门：使用 PowerShell 部署 Azure API for FHIR

本快速入门介绍如何使用 PowerShell 部署 Azure API for FHIR。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>注册 Azure API for FHIR 资源提供程序

如果尚未为订阅注册 `Microsoft.HealthcareApis` 资源提供程序，则可以使用以下命令进行注册：

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-group"></a>创建 Azure 资源组

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-azure-api-for-fhir"></a>部署 Azure API for FHIR

```azurepowershell-interactive
New-AzHealthcareApisService -Name nameoffhirservice -ResourceGroupName myResourceGroupName -Location westus2 -Kind fhir-R4
```

> [!NOTE]
> 根据安装的 `Az` PowerShell 模块的版本，预配 FHIR 服务器可能配置为使用[本地 RBAC](configure-local-rbac.md)，并在已部署 FHIR 服务的允许标识对象 ID 列表中设置了当前登录的 PowerShell 用户。 接下来，建议[使用 Azure RBAC](configure-azure-rbac.md) 来分配数据平面角色，并且可能需要在部署后删除此用户对象 ID，以启用 Azure RBAC 模式。


## <a name="fetch-capability-statement"></a>提取功能语句

你能够通过提取 FHIR 功能语句来验证 Azure API for FHIR 帐户是否正在运行：

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri "https://nameoffhirservice.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除资源组：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupName
```

## <a name="next-steps"></a>后续步骤

在本快速入门指南中，你已将 Azure API for FHIR 部署到订阅中。 若要在 Azure API for FHIR 中设置其他设置，请转到其他设置操作指南。

>[!div class="nextstepaction"]
>[Azure API for FHIR 中的其他设置](azure-api-for-fhir-additional-settings.md)
