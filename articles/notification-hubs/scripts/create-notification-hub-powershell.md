---
title: 使用 PowerShell 创建 Azure 通知中心 |Microsoft Docs
description: 了解如何使用 PowerShell 脚本创建 Azure 通知中心。
author: dimazaid
manager: femila
services: notification-hubs
editor: sethmanheim
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2020
ms.author: dimazaid
ms.openlocfilehash: 01749e1ae5c29a7e77b2143939e25cf1ccd1aec2
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263961"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>使用 PowerShell 创建 Azure 通知中心

此示例 PowerShell 脚本可创建示例 Azure 通知中心。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>必备组件

* **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]

## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可以使用以下命令删除资源组以及与其关联的所有资源：

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzNotificationHubsNamespace](/powershell/module/az.notificationhubs/new-aznotificationhubsnamespace) | 为通知中心创建命名空间。 |
| [New-AzNotificationHub](/powershell/module/az.notificationhubs/new-aznotificationhub) | 创建通知中心。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。
