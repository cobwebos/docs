---
title: 使用 Azure PowerShell 创建 Azure 自定义资源提供程序
description: 介绍如何使用 Azure PowerShell 创建 Azure 自定义资源提供程序
author: MSEvanhi
ms.author: evanhi
ms.topic: quickstart
ms.devlang: azurepowershell
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6912f7f6fdc88c5d611bfbfd78f15e5f7a949f70
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951836"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建 Azure 自定义资源提供程序

本快速入门介绍如何使用 [Az.CustomProviders](/powershell/module/az.customproviders) PowerShell 模块创建你自己的 Azure 自定义资源提供程序。

> [!CAUTION]
> Azure 自定义提供程序目前提供公共预览版。 此预览版本未提供服务级别协议。 对于生产型工作负荷，不建议使用该预览版。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="requirements"></a>要求

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

如果选择在本地使用 PowerShell，则本文要求安装 Az PowerShell 模块，并使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 如果你选择使用 Cloud Shell，请参阅 [Azure Cloud Shell 概述](../../cloud-shell/overview.md)来了解详细信息。

> [!IMPORTANT]
> 尽管 **Az.CustomProviders** PowerShell 模块为预览版，但必须使用 `Install-Module` cmdlet 单独安装它。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中原生提供。

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定订阅。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建 [Azure 资源组](../../azure-resource-manager/management/overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在指定位置创建具有指定名称的资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>创建自定义资源提供程序

若要创建或更新自定义资源提供程序，请使用 [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider) cmdlet，如以下示例所示。

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>获取自定义资源提供程序清单

若要检索有关自定义资源提供程序清单的信息，请使用 [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) cmdlet，如以下示例所示。

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>创建关联

若要创建或更新关联，请使用 [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation) cmdlet，如以下示例所示。

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>获取关联

若要检索有关关联的信息，请使用 [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation) cmdlet，如以下示例所示。

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用本文中创建的资源，可以运行以下示例将其删除。

### <a name="delete-an-association"></a>删除关联

若要删除关联，请使用 [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation) cmdlet。 以下示例将删除一个关联。

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>删除自定义资源提供程序

若要删除自定义资源提供程序，请使用 [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider) cmdlet。 以下示例将删除一个自定义资源提供程序。

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>删除资源组

> [!CAUTION]
> 以下示例删除指定的资源组及其包含的所有资源。
> 如果指定的资源组中存在本文范围外的资源，这些资源也会被删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 自定义资源提供程序](overview.md)。