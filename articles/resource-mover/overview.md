---
title: 什么是 Azure 资源转移器？
description: 了解 Azure 资源转移器
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 621c5e7b0061ccd76fd0109552107915b943511f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653263"
---
# <a name="what-is-azure-resource-mover"></a>什么是 Azure 资源转移器？

本文概述了 Azure 资源转移器服务。 资源转移器帮助你在 Azure 区域之间移动 Azure 资源。

你可以将资源移动到不同的 Azure 区域，以便：

- **与某个区域启动进行协调**：将资源移动到之前未提供的新引入的 Azure 区域。
- **针对服务/功能进行协调**：移动资源以利用特定区域中提供的服务或功能。
- **应对业务发展**：将资源移动到某个区域，以应对业务变化，例如合并或收购。
- **针对临近进行协调**：将资源移动到你的业务所在的本地区域。
- **满足数据要求**：移动资源，使之符合数据驻留要求或数据分类需求。
- **应对部署要求**：移动出错的已部署资源，或者通过移动来应对容量需求。
- **应对停止使用**：因为某个区域已停止使用而移动资源。

> [!IMPORTANT]
> Azure 资源转移器目前提供公共预览版。

## <a name="why-use-resource-mover"></a>为何使用资源转移器？

资源转移器提供以下功能：

- 跨区域移动资源的单一中心。
- 缩短了移动时间并降低了复杂性。 你所需要的一切都在同一个位置。
- 移动不同类型的 Azure 资源的简单、一致的体验。
- 标识要移动的资源之间的依赖项的一种简单方法。 这有助于将相关资源移动到一起，以便在移动后，所有资源在目标区域中都按预期方式工作。
- 如果要在移动后删除源区域中的资源，请在源区域中自动清除它们。
- 测试： 如果不想进行完整移动，可以尝试移动，然后将其丢弃。

## <a name="move-across-regions"></a>跨区域移动

若要跨区域移动资源，请选择要移动的资源。 资源转移器对这些资源进行验证，并解决它们对其他资源的依赖关系。 如果存在依赖项，则你有以下几种选择：
- 将依赖项资源移动到目标区域。
- 不移动依赖项资源，而是改用目标区域中的等效资源。

在解决所有依赖项后，资源转移器会引导你完成一个简单的移动过程。

1. 启动初始移动。
2. 在初始移动后，你可以决定是提交并完成移动，还是放弃移动。
3. 在移动完成后，你可以决定是否要删除源位置中的资源。

你可以在资源转移器中心跨区域移动资源，也可以在资源组内进行移动。 [了解详细信息](select-move-tool.md) 

## <a name="what-resources-can-i-move-across-regions"></a>可以跨区域移动哪些资源？

使用资源转移器，当前可以在区域之间移动以下资源：

- Azure VM 和关联的磁盘
- NIC
- 可用性集 
- Azure 虚拟网络 
- 公共 IP 地址
- 网络安全组 (NSG)：
- 内部和公共负载均衡器 
- Azure SQL 数据库和弹性池


## <a name="next-steps"></a>后续步骤

[详细了解](about-move-process.md)资源转移器组件和移动过程。
