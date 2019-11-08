---
title: Azure 防火墙管理器预览版部署概述
description: 了解 Azure 防火墙管理器预览版所需的高级部署步骤
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488253"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Azure 防火墙管理器预览版部署概述

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

部署 Azure 防火墙管理器预览版有多种方式，但建议执行以下常规过程。

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 必须使用 `Register-AzProviderFeature` PowerShell 命令显式启用 Azure 防火墙管理器预览版。
>在 PowerShell 命令提示符下，运行以下命令：
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>功能注册最多需要 30 分钟即可完成。 运行以下命令来检查注册状态：
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>常规部署过程

1. 创建中心辐射型体系结构

   - 使用 Azure 防火墙管理器创建安全虚拟中心并添加虚拟网络连接。<br>*or*<br>
   - 创建虚拟 WAN 中心并添加虚拟网络连接。
2. 选择安全提供程序

   - 创建安全虚拟中心时完成。<br>*or*<br>
   - 将现有的虚拟 WAN 中心转换为安全虚拟中心。
3. 创建防火墙策略并将其与中心关联

   - 仅在使用 Azure 防火墙时适用。
   - 第三方安全即服务 (SECaaS) 策略通过合作伙伴管理体验配置。
4. 配置路由设置，以将流量路由到安全中心

   - 使用“安全虚拟中心路由设置”页，轻松地将流量路由到安全中心，以便在辐射虚拟网络不具有用户定义路由 (UDR) 的情况下进行筛选和日志记录。

> [!NOTE]
> - 每个区域的每个虚拟 WAN 最多只有一个中心。 但可以在区域中添加多个虚拟 WAN 来实现多个中心。
> - 在 vWAN 中，多个中心不能具有重叠的 IP 空间。
> - 中心 VNet 连接必须与中心位于同一区域。

## <a name="next-steps"></a>后续步骤

- [教程：配合使用 Azure 防火墙管理器预览版和 Azure 门户以保护云网络](secure-cloud-network.md)