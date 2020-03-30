---
title: 还原已删除的应用
description: 了解如何在 Azure 应用服务中还原已删除的应用。 避免意外删除应用带来的麻烦。
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c7d778a0afca4b3552976526d58a2cb2efe12161
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75689620"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>使用 PowerShell 还原已删除的应用服务应用

如果意外删除了 Azure 应用服务中的应用，可以使用 [Az PowerShell 模块](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)中的命令还原它。

> [!NOTE]
> 已删除的应用在初始删除后 30 天内从系统中清除。 清除应用后，无法恢复该应用。
>

## <a name="re-register-app-service-resource-provider"></a>重新注册应用服务资源提供程序
某些客户可能会遇到检索已删除应用列表失败的问题。 若要解决此问题，请运行以下命令：

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

- **已删除的SiteId**：应用的唯一标识符，用于删除多个同名应用的方案
- **订阅 ID**：包含已删除资源的订阅
- **位置**： 原始应用程序的位置
- **资源组名称**：原始资源组的名称
- **名称**：原始应用的名称。
- **Slot**：槽的名称。
- **删除时间**：何时删除应用  

## <a name="restore-deleted-app"></a>还原已删除的应用

确定想要还原的应用后，可以使用 `Restore-AzDeletedWebApp` 来还原它。

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

命令的输入为：

- **资源组**：将还原应用的目标资源组
- **名称**：应用的名称，应全局唯一。
- **目标应用服务计划名称**：链接到应用的应用服务计划

默认情况下，`Restore-AzDeletedWebApp` 会同时还原应用的配置和内容。 如果只想还原内容，请在此 cmdlet 中使用 `-RestoreContentOnly` 标志。

> [!NOTE]
> 如果应用托管在应用服务环境中，后来已将其删除，则仅当相应的应用服务环境仍然存在时，才能还原该应用。
>

您可以在此处找到完整的命令引用：[还原-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp)。
