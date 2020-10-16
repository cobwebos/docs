---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 11d2172d085fe9b47587f4084908f99d7b54437e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103231"
---
在 Azure 门户中创建经典 VNet 时，看到的名称不是用于 PowerShell 的完整名称。 例如，在门户中命名为 **TestVNet1** 的 VNet 在网络配置文件中可能具有更长的名称。 对于资源组 "ClassicRG" 中的 VNet，名称可能如下所示： **Group ClassicRG TestVNet1**。 在创建连接时，请务必使用在网络配置文件中看到的值。

在下面的步骤中，将连接到 Azure 帐户并下载和查看网络配置文件来获取连接所需的值。

1. 下载和安装最新版本的 Azure 服务管理 (SM) PowerShell cmdlet。 大多数人都在本地安装了资源管理器模块，但没有服务管理模块。 服务管理模块是旧版的，必须单独安装。 有关详细信息，请参阅 [安装服务管理 cmdlet](/powershell/azure/servicemanagement/install-azure-ps)。

1. 使用提升的权限打开 PowerShell 控制台，并连接到帐户。 使用以下示例来帮助你进行连接。 必须使用 PowerShell 服务管理模块在本地运行这些命令。 连接到帐户。 使用下面的示例来帮助连接：

   ```powershell
   Add-AzureAccount
   ```
1. 检查该帐户的订阅。

   ```powershell
   Get-AzureSubscription
   ```
1. 如果有多个订阅，请选择要使用的订阅。

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. 在计算机上创建一个目录。 例如，C:\AzureVNet
1. 将网络配置文件导出到目录。 在此示例中，网络配置文件导出到 **C:\AzureNet**。

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. 使用文本编辑器打开该文件，并查看 VNet 和站点的名称。 这些名称将是创建连接时使用的名称。<br>**VNet**名称以**VirtualNetworkSite name =** 形式列出<br>**站点**名称以**LocalNetworkSiteRef name =** 形式列出