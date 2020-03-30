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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774219"
---
在开始配置之前，安装并导入所需的模块。 您需要管理员权限才能在 PowerShell 中安装模块。

1. 安装和导入 Az 模块
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. 安装和导入 Az.对等模块
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 使用下面的命令验证模块导入正常。
    ```powershell
    Get-Module
    ```
1. 使用以下命令登录到 Azure 帐户。
    ```powershell
    Connect-AzAccount
    ```
1. 检查帐户的订阅并选择要在其中创建对等互连的订阅。
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 如果还没有资源组，则必须在创建对等互连之前创建一个资源组。 为此，可以运行以下命令：

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> 如果尚未关联 ASN 和订阅，请按照[关联对等 ASN](../howto-subscription-association-powershell.md)的步骤操作。 这是请求对等互连所必需的。

> [!NOTE]
> 资源组的位置与选择设置对等互连的位置无关。
&nbsp;
