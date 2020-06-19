---
title: 还原已删除的应用
description: 了解如何还原 Azure 应用服务中的已删除应用。 避免意外删除应用带来的麻烦。
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 440f46cbeebee1b552e64eba4ebc8787a47edf56
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779210"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>使用 PowerShell 还原已删除的应用服务应用

如果意外删除了 Azure 应用服务中的应用，则可以使用 [Az PowerShell 模块](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)中的命令将其还原。

> [!NOTE]
> 在初始删除后的 30 天过后，将从系统中清除已删除的应用。 应用一旦清除，将无法恢复。
>

## <a name="re-register-app-service-resource-provider"></a>重新注册应用服务资源提供程序
一些客户可能会遇到检索已删除应用列表失败的问题。 若要解决该问题，请运行以下命令：

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>列出已删除的应用

若要获取已删除应用的集合，可使用 `Get-AzDeletedWebApp`。

如需特定已删除应用的详细信息，可使用：

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

详细信息包括：

- **DeletedSiteId**：应用的唯一标识符，用于已删除具有相同名称的多个应用的方案
- **SubscriptionID**：包含已删除资源的订阅
- **位置**：原始应用的位置
- **ResourceGroupName**：原始资源组的名称
- **Name**：原始应用的名称。
- **Slot**：槽的名称。
- **Deletion Time**：删除应用的时间  

## <a name="restore-deleted-app"></a>还原已删除的应用
>[!NOTE]
> 函数应用不支持 `Restore-AzDeletedWebApp`。

确定要还原的应用后，可使用 `Restore-AzDeletedWebApp` 进行还原。

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> 部署槽位不会作为应用的一部分还原。 如果需要还原过渡槽，请使用 `-Slot <slot-name>` 标志。
>

命令的输入包括：

- **资源组**：将在其中还原应用的目标资源组
- **Name**：应用的名称，应全局唯一。
- **TargetAppServicePlanName**：链接到应用的应用服务计划

默认情况下，`Restore-AzDeletedWebApp` 将同时还原应用配置和内容。 如果只想还原内容，请将 `-RestoreContentOnly` 标志与此 commandlet 配合使用。

> [!NOTE]
> 如果应用托管在应用服务环境中并随后从应用服务环境中删除，则仅当相应应用服务环境仍然存在时，才可将其还原。
>

可在此处找到完整的 commandlet 参考：[Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp)。
