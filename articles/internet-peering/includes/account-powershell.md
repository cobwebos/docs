---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774219"
---
开始配置之前，请安装并导入所需的模块。 你将需要管理员权限才能在 PowerShell 中安装模块。

1. 安装和导入 Az module
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. 安装和导入 Az. 对等模块
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 使用以下命令验证是否已将模块导入正确。
    ```powershell
    Get-Module
    ```
1. 使用以下命令登录到 Azure 帐户。
    ```powershell
    Connect-AzAccount
    ```
1. 检查帐户的订阅，并选择要在其中创建对等互连的订阅。
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 如果还没有资源组，则必须先创建一个资源组，然后才能创建对等互连。 为此，可以运行以下命令：

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> 如果尚未关联 ASN 和订阅，请遵循[关联对等 asn](../howto-subscription-association-powershell.md)的步骤。 这对于请求对等互连是必需的。

> [!NOTE]
> 资源组的位置与选择设置对等互连的位置无关。
&nbsp;
