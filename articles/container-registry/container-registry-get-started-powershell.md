---
title: "Azure 容器注册表存储库 | Microsoft Docs"
description: "如何使用 Azure 容器注册表存储库存储 Docker 映像"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: cristyg
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 1e5d5ea5b1ec121fe008abc48178b1d58f540ce1
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-powershell"></a>使用 Azure PowerShell 创建专用 Docker 容器注册表
使用 [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview) 中的命令从 Windows 计算机创建容器注册表并管理其设置。 也可以使用 [Azure 门户](container-registry-get-started-portal.md)、[Azure CLI](container-registry-get-started-azure-cli.md) 或者使用容器注册表 [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) 以编程方式创建和管理容器注册表。


* 有关背景和概念，请参阅[概述](container-registry-intro.md)
* 有关支持的 cmdlet 的完整列表的信息，请参阅 [Azure 容器注册表管理 Cmdlet](https://docs.microsoft.com/en-us/powershell/module/azurerm.containerregistry/)。


## <a name="prerequisites"></a>先决条件
* **Azure PowerShell**：若要安装和开始使用 Azure PowerShell，请参阅[安装说明](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)。 运行 `Login-AzureRMAccount` 登录到 Azure 订阅。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/en-us/powershell/azure/get-started-azurep)。
* **资源组**：在创建容器注册表之前创建[资源组](../azure-resource-manager/resource-group-overview.md#resource-groups)，或使用现有资源组。 请确保该资源组位于[提供](https://azure.microsoft.com/regions/services/)容器注册表服务的位置。 若要使用 Azure PowerShell 创建资源组，请参阅 [PowerShell 参考](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps#create-a-resource-group)。
* **存储帐户**（可选）：创建一个标准的 Azure [存储帐户](../storage/common/storage-introduction.md)，用于在同一位置备份容器注册表。 如果使用 `New-AzureRMContainerRegistry` 创建注册表时未指定存储帐户，该命令会自动创建一个存储帐户。 若要使用 PowerShell 创建存储帐户，请参阅 [PowerShell 参考](https://docs.microsoft.com/en-us/powershell/module/azure/new-azurestorageaccount)。 当前不支持高级存储。
* **服务主体**（可选）：使用 PowerShell 创建注册表时，默认情况下不会为该注册表设置访问权限。 可以根据需要将现有 Azure Active Directory 服务主体分配到注册表，也可以创建并分配新的服务主体。 或者，可以启用注册表的管理员用户帐户。 请参阅本文稍后的部分。 有关注册表访问权限的详细信息，请参阅 [Authenticate with the container registry](container-registry-authentication.md)（使用容器注册表进行身份验证）。

## <a name="create-a-container-registry"></a>创建容器注册表
运行 `New-AzureRMContainerRegistry` 命令可以创建容器注册表。

> [!TIP]
> 创建注册表时，请指定仅包含字母和数字的全局唯一顶级域名。 示例中的注册表名称为 `MyRegistry`，但需要将它替换成自己的唯一名称。
>
>

以下命令使用少量的参数在美国中南部位置的资源组 `MyResourceGroup` 中创建容器注册表 `MyRegistry`：

```PowerShell
$Registry = New-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

* `-StorageAccountName` 是可选项。 如果未指定，则会创建一个存储帐户，其名称由注册表名称以及指定资源组中的一个时间戳组成。

## <a name="assign-a-service-principal"></a>分配服务主体
使用 PowerShell 命令可将 Azure Active Directory [服务主体](../azure-resource-manager/resource-group-authenticate-service-principal.md)分配到注册表。 为这些示例中的服务主体分配了“所有者”角色，但可以根据需要分配[其他角色](../active-directory/role-based-access-control-configure.md)。

### <a name="create-a-service-principal"></a>创建服务主体
在以下命令中，创建新的服务主体。 使用 `-Password` 参数指定一个强密码。

```PowerShell
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName ApplicationDisplayName -Password "MyPassword"
```

### <a name="assign-a-new-or-existing-service-principal"></a>分配新的或现有的服务主体
可以将新的或现有的服务主体分配到注册表。 若要为它分配对注册表的“所有者”角色访问权限，请运行类似于以下示例的命令：

```PowerShell
New-AzureRMRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Registry.Id
```

##<a name="sign-in-to-the-registry-with-the-service-principal"></a>通过服务主体登录到注册表
将服务主体分配到注册表后，可以使用以下命令登录：

```PowerShell
docker login -u $ServicePrincipal.ApplicationId -p myPassword
```

## <a name="manage-admin-credentials"></a>管理管理员凭据
管理员帐户是为每个容器注册表自动创建的，默认情况下已禁用。 以下示例演示 PowerShell 命令如何管理容器注册表的管理员凭据。

### <a name="obtain-admin-user-credentials"></a>获取管理员用户凭据
```PowerShell
Get-AzureRMContainerRegistryCredential -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

### <a name="enable-admin-user-for-an-existing-registry"></a>为现有注册表启用管理员用户
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -EnableAdminUser
```

### <a name="disable-admin-user-for-an-existing-registry"></a>为现有注册表禁用管理员用户
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -DisableAdminUser
```

## <a name="next-steps"></a>后续步骤
* [使用 Docker CLI 推送第一个映像](container-registry-get-started-docker-cli.md)

