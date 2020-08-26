---
title: 还原已删除的应用
description: 了解如何在 Azure 应用服务中还原已删除的应用。 避免意外删除应用带来的麻烦。
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 20c220bcb44a1a47e308f57d1466aee2773111a4
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985676"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>使用 PowerShell 还原已删除的应用服务应用

如果意外删除了 Azure 应用服务中的应用，则可以使用 [Az PowerShell 模块](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)中的命令将其还原。

> [!NOTE]
> - 初始删除 30 天后，已删除的应用将从系统中清除。 清除应用后，将无法恢复。
> - 消耗计划不支持撤消删除功能。
> - 在应用服务环境中运行的应用服务应用不支持快照。 因此，应用服务环境中运行的应用服务应用不支持删除功能和克隆功能。
>

## <a name="re-register-app-service-resource-provider"></a>重新注册应用服务资源提供程序

有些客户可能会遇到这样的问题：检索已删除的应用的列表时失败。 若要解决该问题，请运行以下命令：

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>列出已删除的应用

若要获取已删除的应用集合，可以使用 `Get-AzDeletedWebApp`。

若要获取有关特定的已删除应用的详细信息，可以使用：

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

详细信息包括:

- **DeletedSiteId**：应用的唯一标识符，适用于删除了多个同名应用的情况
- **SubscriptionID**：包含已删除的资源的订阅
- **位置**：原始应用的位置
- **ResourceGroupName**：原始资源组的名称
- **名称**：原始应用的名称。
- **Slot**：槽的名称。
- **Deletion Time**：删除应用的时间  

## <a name="restore-deleted-app"></a>还原已删除的应用

>[!NOTE]
> 函数应用不支持 `Restore-AzDeletedWebApp`。

确定要还原的应用后，可使用 `Restore-AzDeletedWebApp` 进行还原。

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> 部署槽不会作为应用的一部分进行还原。 如果需要还原过渡槽，请使用 `-Slot <slot-name>` 标志。
>

命令的输入包括：

- **目标资源组**：要在其中还原应用的目标资源组
- **名称**：应用的名称，应全局唯一。
- **TargetAppServicePlanName**：链接到该应用的应用服务计划

默认情况下，`Restore-AzDeletedWebApp` 会同时还原应用配置以及任何内容。 如果只想还原内容，请在此 cmdlet 中使用 `-RestoreContentOnly` 标志。

> [!NOTE]
> 如果应用已在应用服务环境中托管，然后从中删除，则只有当相应应用服务环境仍然存在时，才能还原它。
>

可在以下文章中找到完整的 cmdlet 参考信息：[Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp)。
