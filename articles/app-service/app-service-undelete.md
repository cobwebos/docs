---
title: 还原已删除的应用服务应用-Azure App Service
description: 了解如何使用 PowerShell 还原已删除的应用服务应用。
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: a766f7ed7a82874df8ef7506226de0d6f38a5847
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219533"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>使用 PowerShell 还原已删除的应用服务应用

如果意外删除了 Azure App Service 中的应用，可以使用[Az PowerShell 模块](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)中的命令进行还原。

## <a name="list-deleted-apps"></a>列出已删除的应用

若要获取已删除应用的集合，可以使用`Get-AzDeletedWebApp`。

有关可使用的特定已删除应用的详细信息，请执行以下操作：

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

详细信息包括：

- **DeletedSiteId**：应用的唯一标识符，用于已删除具有相同名称的多个应用的方案
- **SubscriptionID**：包含已删除资源的订阅
- **位置**：原始应用的位置
- **ResourceGroupName**：原始资源组的名称
- **名称**：原始应用程序的名称。
- **槽**：槽的名称。
- **删除时间**：应用删除的时间  

## <a name="restore-deleted-app"></a>还原已删除的应用

确定要还原的应用后，可以使用`Restore-AzDeletedWebApp`还原它。

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

命令的输入为：

- **资源组**：将在其中还原应用的目标资源组
- **名称**：应用的名称，应是全局唯一的。
- **TargetAppServicePlanName**：链接到应用的应用服务计划

默认情况`Restore-AzDeletedWebApp`下，会还原应用配置以及内容。 如果只想还原内容，请对此 commandlet 使用`-RestoreContentOnly`标志。
