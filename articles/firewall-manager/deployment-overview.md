---
title: Azure 防火墙管理器部署概述
description: 了解 Azure 防火墙管理器所需的高级部署步骤
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: ceb6e84b31067f7289b9e003a4fb273ce717de33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079092"
---
# <a name="azure-firewall-manager-deployment-overview"></a>Azure 防火墙管理器部署概述

部署 Azure 防火墙管理器有多种方式，但建议执行以下常规过程。

## <a name="general-deployment-process"></a>常规部署过程

### <a name="hub-virtual-networks"></a>中心虚拟网络

1.  创建防火墙策略

    - 创建新策略
<br>*or*<br>
    - 派生基本策略并自定义本地策略
<br>*or*<br>
    - 导入现有 Azure 防火墙中的规则。 确保从应跨多个防火墙应用的策略中删除 NAT 规则
1. 创建中心辐射型体系结构
   - 使用 Azure 防火墙管理器创建中心虚拟网络，并使用虚拟网络对等互连将辐射虚拟网络以对等互连方式连接到它
<br>*or*<br>
    - 创建虚拟网络并添加虚拟网络连接，然后使用虚拟网络对等互连将辐射虚拟网络以对等互连方式连接到它

3. 选择安全提供程序并关联防火墙策略。 目前，只有 Azure 防火墙是受支持的提供程序。

   - 这是在创建中心虚拟网络时完成的
<br>*or*<br>
    - 将现有的虚拟网络转换为中心虚拟网络。 还可以转换多个虚拟网络。

4. 配置用户定义的路由，将流量路由到中心虚拟网络防火墙。


### <a name="secured-virtual-hubs"></a>安全虚拟中心

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
>
> 有关更多已知问题，请参阅[什么是 Azure 防火墙管理器？](overview.md#known-issues)

## <a name="convert-virtual-networks"></a>转换虚拟网络

如果将现有虚拟网络转换为中心虚拟网络，则以下信息适用：

- 如果该虚拟网络具有现有的 Azure 防火墙，请选择一个要与现有防火墙关联的防火墙策略。 防火墙策略替换防火墙规则时，防火墙预配状态将为“正在更新”。 在该预配状态期间，防火墙会继续处理流量，不会出现停机时间。 可以使用防火墙管理器或 Azure PowerShell 将现有规则导入到防火墙策略。
- 如果该虚拟网络没有关联的 Azure 防火墙，则会部署一个防火墙并且防火墙策略会与此新防火墙关联。

## <a name="next-steps"></a>后续步骤

- [教程：在 Azure 门户中使用 Azure 防火墙管理器保护云网络](secure-cloud-network.md)