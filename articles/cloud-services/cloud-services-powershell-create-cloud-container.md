---
title: "使用 PowerShell 创建云服务容器 | Microsoft Docs"
description: "本文说明如何使用 PowerShell 创建云服务容器。 该容器承载 Web 角色和辅助角色。"
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: 
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: b55a0dd7800448c50897af784092b4a60fa7a25e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>使用 Azure PowerShell 命令可创建一个空的云服务容器
本文介绍如何使用 Azure PowerShell cmdlet 快速创建云服务容器。 请执行以下步骤：

1. 从 [Azure PowerShell 下载](http://aka.ms/webpi-azps)页安装 Microsoft Azure PowerShell cmdlet。
2. 打开 PowerShell 命令提示符。
3. 使用 [Add-AzureAccount](/powershell/module/Azure/Add-AzureAccount?view=azuresmps-4.0.0) 登录。

   > [!NOTE]
   > 有关安装 Azure PowerShell cmdlet 和连接到 Azure 订阅的更多说明，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。
   >
   >
4. 使用 **New-AzureService** cmdlet 创建一个空的 Azure 云服务容器。

   ```powershell
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. 请按照本示例操作以调用 cmdlet：

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

有关创建 Azure 云服务的详细信息，请运行：

```
Get-help New-AzureService
```

### <a name="next-steps"></a>后续步骤
* 若要管理云服务部署，请参阅 [Get-AzureService](/powershell/module/Azure/Get-AzureService?view=azuresmps-4.0.0)、[Remove-AzureService](/powershell/module/Azure/Remove-AzureService?view=azuresmps-4.0.0) 和 [Set-AzureService](/powershell/module/azure/set-azureservice?view=azuresmps-4.0.0) 命令。 有关更多信息，还可以参阅[如何配置云服务](cloud-services-how-to-configure-portal.md)。
* 若要将云服务项目发布到 Azure，请参见[已存档的云服务存储库](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery)中的 **PublishCloudService.ps1** 代码示例。
