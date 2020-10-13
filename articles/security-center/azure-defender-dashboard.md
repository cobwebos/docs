---
title: Azure Defender 的仪表板及其功能
description: 了解 Azure Defender 仪表板的功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 35469c7a11d47e586187b55bde1e8ad8a0c94f5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448402"
---
# <a name="the-azure-defender-dashboard"></a>Azure Defender 仪表板

Azure Defender 面板提供：

- 跨不同资源类型查看 Azure Defender 覆盖范围
- 用于配置高级威胁防护功能的链接
- 载入状态和代理安装
- Azure Defender 威胁检测警报 

若要访问 Azure Defender 仪表板，请从安全中心菜单的 "云安全性" 部分中选择 " **Azure defender** "。

## <a name="whats-shown-on-the-dashboard"></a>仪表板上显示了哪些内容？

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Azure Defender 仪表板示例" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

该仪表板包含以下部分：

1. **Azure defender 覆盖范围** -可在此处查看订阅中的资源类型，并可通过 azure defender 进行保护。 无论身在何处，你都可以选择升级。 如果要升级所有可能符合条件的资源，请选择 " **全部升级**"。

1. "**安全警报" 区域**-Azure Defender 检测到环境中任何区域的威胁时，会生成警报。 这些警报会描述受影响资源的详细信息、建议的修正步骤，在某些情况下还会提供触发逻辑应用作为响应的选项。 选择此图中的任意位置将打开 " **安全警报" 页**。

1. **高级保护** -Azure Defender 包含许多高级威胁防护功能，适用于虚拟机、SQL 数据库、容器、web 应用程序和网络等。 在此 "高级保护" 部分中，可以查看所选订阅中每种保护的资源的状态。 选择其中任何一个，直接跳到该保护类型的配置区域。

1. **Insights** -此新闻滚动窗格新闻、建议阅读和高优先级警报使安全中心深入了解与你和你的订阅相关的安全问题。 无论是通过漏洞分析工具在 Vm 上发现的高严重性标识符列表，还是安全中心团队成员提供的新博客文章，都可以在 **Azure Defender 仪表板**的 "见解" 窗格中找到该列表。




## <a name="next-steps"></a>后续步骤

本文介绍了 Azure Defender 仪表板。 

有关 Azure Defender 的详细信息，请参阅 [Azure Defender 简介](azure-defender.md)

> [!div class="nextstepaction"]
> [启用 Azure Defender](security-center-pricing.md)