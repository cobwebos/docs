---
title: Azure 安全中心的定价
description: Azure 安全中心在两种模式下提供，且没有 Azure Defender。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 393e769f147629d2d40344052ba502b22a18cd2a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314532"
---
# <a name="pricing-of-azure-security-center"></a>Azure 安全中心的定价
Azure 安全中心为 Azure、本地和其他云中运行的工作负载提供统一的安全管理和高级威胁防护功能。 它可以提供针对混合云工作负载的可见性和可控性、可减小在威胁下的曝光面的积极防御功能以及有助于随时响应快速演变的网络攻击的智能检测功能。


## <a name="free-option-vs-azure-defender-enabled"></a>免费选项 vs Azure Defender 已启用

安全中心提供两种模式：

- 当你首次在 Azure 门户中访问 Azure 安全中心仪表板时， **Azure defender** (免费) -不支持 azure Defender 的安全中心在所有 azure 订阅上免费启用，或者如果是通过 API 以编程方式启用，则为。 使用此免费模式提供安全策略、持续的安全评估和可操作的安全建议，以帮助你保护 Azure 资源。

- 启用 azure Defender 后 **，Azure defender**会将免费模式的功能扩展到在私有云和其他公有云中运行的工作负荷，并跨混合云工作负荷提供统一的安全管理和威胁防护。 Azure Defender 的一些主要功能：

    - 混合安全 – 在所有本地和云工作负载上获得统一的安全视图。 应用安全策略并持续评估混合云工作负载的安全性，确保符合安全标准。 收集、搜索并分析来自多个源（包括防火墙和其他合作伙伴解决方案）的安全数据。
    - **威胁防护警报** -高级行为分析和 Microsoft Intelligent Security Graph 提供了一种覆盖不断演变的网络攻击的优势。 利用内置行为分析和机器学习来识别攻击和零时差攻击。 监视网络、计算机和云服务是否出现有即将来袭的攻击和攻破后活动。 使用交互工具和上下文威胁智能简化调查。
    - **虚拟机和容器注册表的漏洞扫描** -可轻松地将扫描仪部署到为漏洞管理提供业界最先进解决方案的所有虚拟机。 直接在安全中心内查看、调查和修复扫描到的漏洞。 
    - **访问和应用程序控件** -通过应用适合于特定工作负荷的机器学习驱动建议来创建允许列表和拒绝列表，阻止恶意软件和其他不需要的应用程序。 实时减小网络受攻击面，控制对 Azure VM 上的管理端口的访问。 这会显著减小在暴力和其他网络攻击下的曝光面。
    - **容器安全功能** - 获得在容器化环境中进行漏洞管理和实时威胁保护的好处。 **为容器注册表启用 Azure Defender**时，可能需要12hrs 到所有功能都已启用。 根据推送到已连接注册表的唯一容器映像的数量收费。 扫描映像一次后，不会再对其收费，除非已对其进行了多次修改和推送。 

## <a name="try-azure-defender-free-for-30-days"></a>免费试用 Azure Defender 30 天

在前30天内，Azure Defender 免费。 30天结束后，如果你选择继续使用该服务，我们将自动开始充电以供使用。

## <a name="enable-azure-defender"></a>启用 Azure Defender

你可以使用 Azure Defender 保护整个 Azure 订阅，且该保护将由订阅中的所有资源继承。

若要启用 Azure Defender：

1. 从安全中心的主菜单中，选择 " **定价 & 设置**"。
1. 选择要升级的订阅。
1. 选择要升级 **的 Azure Defender** 。
1. 选择“保存”。

下面是示例订阅的定价页。 你会注意到，Azure Defender 中的每个计划都单独定价，可以单独设置为 on 或 off。

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="门户中安全中心的定价页":::

> [!NOTE]
> 若要启用包括威胁防护功能在内的所有安全中心功能，必须在包含适用工作负荷的订阅上启用 Azure Defender。 如果在工作区级别启用此项，则不会为 Azure 资源启用实时 VM 访问、自适应应用程序控件和网络检测。 
>
> 可以在订阅级别或资源级别 **为存储帐户启用 Azure Defender** 。
> 可以在订阅级别或资源级别启用 **适用于 SQL 的 Azure Defender** 。
> 只能在资源级别为 Azure Database for MariaDB/MySQL/PostgreSQL 启用威胁防护。


## <a name="next-steps"></a>后续步骤
本文介绍了安全中心定价。 有关相关材料，请参阅：

- [如何优化 Azure 工作负荷成本](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [根据所选货币和所在区域的定价详细信息](https://azure.microsoft.com/pricing/details/security-center/)
- 你可能希望管理成本，并通过将解决方案的应用范围限制为特定的一组代理来限制为解决方案收集的数据量。 [解决方案目标](../operations-management-suite/operations-management-suite-solution-targeting.md)使用户可以向解决方案应用一个范围，并可将目标设定为工作区中的一个计算机子集。 如果使用解决方案目标功能，安全中心会将工作区列为没有解决方案。